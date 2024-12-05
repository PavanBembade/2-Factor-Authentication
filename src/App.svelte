<script>
  import "./App.css";

  // Constants
  const PASS_CODE = "123456";
  const OTP_LENGTH = 6;

  // State variables
  let otp = Array(OTP_LENGTH).fill("");
  let wrongOtp = false;
  let rightOtp = false;

  // Reactive variables
  $: otpCode = otp.join("");
  $: hideDiv = otpCode.length === OTP_LENGTH;
  $: remainingValCount = OTP_LENGTH - otpCode.length;

  // Handle input for each OTP box
  function handleInput(event, index) {
    const value = event.target.value;

    if (/^[0-9]$/.test(value)) {
      otp[index] = value;

      if (index < OTP_LENGTH - 1) {
        document.getElementById(`otp-input-${index + 1}`).focus();
      }
    } else {
      otp[index] = "";
    }
    validateOtp();
  }

  // Navigate inputs with arrow keys or backspace
  function handleKeydown(event, index) {
    const navigate = (nextIndex) => {
      const nextInput = document.getElementById(`otp-input-${nextIndex}`);
      if (nextInput) {
        nextInput.focus();
      }

      // Ensure the nextInput is an input element before calling select()
      if (nextInput instanceof HTMLInputElement) {
        setTimeout(() => nextInput.select(), 0);
      }
    };

    if (event.key === "Backspace" && index > 0 && !otp[index]) {
      navigate(index - 1);
    } else if (
      event.key === "ArrowRight" ||
      (event.key === "ArrowUp" && index < OTP_LENGTH - 1)
    ) {
      navigate(index + 1);
    } else if (
      event.key === "ArrowLeft" ||
      (event.key === "ArrowDown" && index > 0)
    ) {
      navigate(index - 1);
    }
  }
  // Automatically select value on focus
  function handleFocus(event) {
    event.target.select();
  }

  // Handle pasting an OTP
  function handlePaste(event) {
    const pasteData = event.clipboardData.getData("text").trim();

    if (/^\d+$/.test(pasteData)) {
      const digits = pasteData.slice(0, OTP_LENGTH).split("");

      // Populate OTP inputs
      digits.forEach((digit, i) => (otp[i] = digit));
      otp = [...otp];
      otpCode = otp.join("");
      hideDiv = otpCode.length === OTP_LENGTH;

      validateOtp();

      // Focus the first empty input or last input
      const nextFocusIndex =
        digits.length < OTP_LENGTH ? digits.length : OTP_LENGTH - 1;
      document.getElementById(`otp-input-${nextFocusIndex}`).focus();
    }

    event.preventDefault();
  }

  // Validate the OTP
  function validateOtp() {
    wrongOtp = hideDiv && otpCode !== PASS_CODE;
    rightOtp = hideDiv && otpCode === PASS_CODE;
  }
</script>

<div class="grid grid-cols-12 bg-blue-200 h-screen w-full">
  <div
    class="col-start-2 col-span-10 lg:col-start-4 lg:col-span-5 xl:col-start-5 xl:col-span-4 flex justify-center items-center w-full relative"
  >
    <div
      class="bg-white p-8 rounded-[1.5rem] min-w-[320px] w-[500px] shadow-xl z-10"
    >
      <div class="flex flex-col gap-8">
        <!-- Icon and Heading -->
        <div class="flex flex-col gap-3 text-center">
          <div class="flex justify-center">
            <span
              class="material-symbols-outlined text-[3.5rem] p-[2.5rem] rounded-full
              {wrongOtp
                ? 'animate-pulse text-red-400 bg-red-400/[0.13]'
                : 'text-sky-400 bg-blue-400/[0.13]'} 
              {rightOtp
                ? 'animate-pulse text-green-400 bg-green-400/[0.13]'
                : ''}"
            >
              {rightOtp ? "lock_open_right" : "lock"}
            </span>
          </div>
          <h3 class="font-semibold text-[2.2rem] leading-[1.2]">Easy Peasy</h3>
          <p class="text-sm text-gray-500">
            Enter your 6-digit code from your 2FA app
          </p>
        </div>

        <!-- OTP Inputs -->
        <div class="flex justify-center gap-4">
          {#each otp as digit, index}
            <input
              id={`otp-input-${index}`}
              class="col-span-1 min-w-[30px] w-[60px] text-center border text-3xl py-[1rem] rounded-lg focus:outline-none
              {wrongOtp
                ? 'shake focus:border-red-500 focus:ring-1 focus:ring-red-500'
                : 'focus:border-sky-500 focus:ring-1 focus:ring-sky-500'} 
              {digit
                ? wrongOtp
                  ? 'border-red-500 text-red-500 bg-red-200/[0.10]'
                  : 'border-sky-500 text-sky-600'
                : ''}"
              type="text"
              autocomplete="off"
              maxlength="1"
              style={index === 3 ? "margin-left: 1rem;" : ""}
              inputmode="numeric"
              bind:value={otp[index]}
              on:input={(event) => handleInput(event, index)}
              on:keydown={(event) => handleKeydown(event, index)}
              on:focus={handleFocus}
              on:paste={handlePaste}
            />
          {/each}
        </div>

        <!-- Remaining Digits Message -->
        {#if !hideDiv}
          <div class="w-full border p-5 bg-gray-200 rounded-lg text-center">
            <button class="font-bold text-lg text-gray-500"
              >{remainingValCount} digits left</button
            >
          </div>
        {/if}

        <!-- Error Message -->
        {#if wrongOtp}
          <div
            class="w-full border p-5 bg-red-400 text-center rounded-lg shake"
          >
            <button class="font-bold text-lg text-white">Wrong Code</button>
          </div>
        {/if}

        <!-- Success Message -->
        {#if rightOtp}
          <div
            class="w-full button border p-5 text-center rounded-lg"
            style="animation: gradientAnimation 0.3s ease-out forwards; "
          >
            <button class="font-bold text-lg text-white">Let's Go</button>
          </div>
        {/if}
      </div>
    </div>
  </div>
</div>
