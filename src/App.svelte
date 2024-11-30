<script>
  import "./App.css";

  // Constants
  const PASS_CODE = "123456";
  const OTP_LENGTH = 6;

  // State
  let otp = Array(OTP_LENGTH).fill("");
  let otpCode = "";
  let wrongOtp = false;
  let rightOtp = false;
  let hideDiv = false;

  // Reactive state
  $: remainingValCount = otp.filter((value) => !value).length;
  $: otpCode = otp.join("");
  $: hideDiv = otpCode.length === OTP_LENGTH;

  // Handle input
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

    checkOtpValidity();
  }

  // Handle backspace navigation
  function handleKeydown(event, index) {
    if (event.key === "Backspace" && index > 0 && !otp[index]) {
      document.getElementById(`otp-input-${index - 1}`).focus();
    }
  }

  // Prevent non-numeric input
  function handleKeypress(event) {
    if (!/[0-9]/.test(event.key)) {
      event.preventDefault();
    }
  }

  // Validate OTP
  function checkOtpValidity() {
    wrongOtp = hideDiv && otpCode !== PASS_CODE;
    rightOtp = hideDiv && otpCode === PASS_CODE;
  }
</script>

<div class="grid grid-cols-12 bg-blue-200 h-screen w-full">
  <div class="col-start-2 col-span-10 lg:col-start-4 lg:col-span-5 xl:col-start-5 xl:col-span-4 flex items-center w-full relative">
    <div class="bg-white p-6 rounded-2xl w-full shadow-xl z-10">
      <div class="flex flex-col gap-8">
        <!-- Icon and Heading -->
        <div class="flex flex-col gap-2 text-center">
          <div class="flex justify-center">
            <span
              class="material-symbols-outlined text-[50px] p-10 rounded-full 
              {wrongOtp ? 'animate-pulse text-red-400 bg-red-400/[0.13]' : 'text-sky-400 bg-blue-400/[0.13]'} 
              {rightOtp ? 'animate-pulse text-green-400 bg-green-400/[0.13]' : ''}"
            >
              {rightOtp ? "lock_open_right" : "lock"}
            </span>
          </div>
          <h3 class="font-semibold text-2xl">Easy Peasy</h3>
          <p class="text-sm opacity-50">Enter your 6-digit code from your 2FA app</p>
        </div>

        <!-- OTP Inputs -->
        <div class="grid grid-cols-6 gap-4">
          {#each otp as digit, index}
            <input
              id={`otp-input-${index}`}
              class="col-span-1 text-center border text-xl py-3 rounded-lg focus:outline-none 
              {wrongOtp ? 'shake focus:border-red-500 focus:ring-1 focus:ring-red-500' : 'focus:border-sky-500 focus:ring-1 focus:ring-sky-500'} 
              {digit ? (wrongOtp ? 'border-red-500 text-red-800 bg-red-200/[0.10]' : 'border-sky-500 text-sky-600') : ''}"
              type="text"
              maxlength="1"
              inputmode="numeric"
              bind:value={otp[index]}
              on:input={(event) => handleInput(event, index)}
              on:keydown={(event) => handleKeydown(event, index)}
              on:keypress={handleKeypress}
            />
          {/each}
        </div>

        <!-- Remaining Digits btn -->
        {#if !hideDiv}
          <div class="w-full border p-4 bg-gray-200 rounded-2xl text-center">
            <button class="font-bold text-xl opacity-50">{remainingValCount} digits left</button>
          </div>
        {/if}

        <!-- Error Message btn-->
        {#if wrongOtp}
          <div class="w-full border p-4 bg-red-400 text-center rounded-2xl shake">
            <button class="font-bold text-xl text-white">Wrong Code</button>
          </div>
        {/if}

        <!-- Success Message btn-->
        {#if rightOtp}
          <div class="w-full button border p-4 text-center rounded-2xl">
            <button class="font-bold text-xl text-white">Let's Go</button>
          </div>
        {/if}
      </div>
    </div>
  </div>
</div>
