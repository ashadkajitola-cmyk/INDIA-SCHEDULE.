<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Cricket Central - Admin Dashboard & Play Billing</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-slate-950 text-slate-100 flex flex-col font-sans min-h-screen">
  <div id="root">
    <!-- Header Navbar -->
    <header class="bg-slate-900 border-b border-slate-800 sticky top-0 z-50 px-4 py-3 flex items-center justify-between shadow-md">
      <div class="flex items-center space-x-3 cursor-pointer">
        <span class="text-xl">←</span>
        <span class="font-bold text-lg tracking-wide text-transparent bg-clip-text bg-gradient-to-r from-blue-400 to-indigo-400">Cricket Central</span>
      </div>
      <div class="flex items-center space-x-2">
        <button onclick="switchTab('matches')" id="tabMatches" class="px-3 py-1.5 text-xs font-semibold rounded-lg bg-blue-600 text-white shadow">MATCHES</button>
        <button onclick="switchTab('points')" id="tabPoints" class="px-3 py-1.5 text-xs font-semibold rounded-lg bg-slate-800 text-slate-300 hover:bg-slate-700">POINTS TABLE</button>
        <button onclick="switchTab('groups')" id="tabGroups" class="px-3 py-1.5 text-xs font-semibold rounded-lg bg-slate-800 text-slate-300 hover:bg-slate-700">GROUPS</button>
        <button onclick="openSubscriptionModal()" class="px-3 py-1.5 text-xs font-semibold rounded-lg bg-amber-600 text-white shadow hover:bg-amber-500">⭐ Subscription</button>
      </div>
    </header>

    <!-- Google Play Billing / Top-Up Modal (Hidden text about free number as requested) -->
    <div id="subModal" class="fixed inset-0 bg-black/85 z-50 flex items-center justify-center p-4 hidden">
      <div class="bg-slate-900 border border-slate-800 rounded-2xl p-6 max-w-md w-full shadow-2xl relative">
        <div class="flex items-center justify-between mb-2">
          <h3 class="text-xl font-bold text-amber-400 flex items-center gap-2">Google Play Billing</h3>
          <span class="text-xs bg-emerald-950 text-emerald-400 border border-emerald-800 px-2 py-0.5 rounded-full">Secure Top-Up</span>
        </div>
        <p class="text-xs text-slate-400 mb-4">Funds will be credited via Google Play Balance to developer: <strong class="text-slate-200">Ashadkazitola@gmail.com</strong></p>

        <!-- Subscription Plans -->
        <div class="space-y-3 mb-6">
          <label class="flex items-center justify-between p-3 bg-slate-950 border border-slate-800 rounded-xl cursor-pointer hover:border-amber-500 transition-all">
            <div>
              <span class="font-bold text-sm block text-slate-200">Weekly Plan</span>
              <span class="text-xs text-slate-400">Valid for 7 Days</span>
            </div>
            <span class="text-amber-400 font-bold text-base">₹15</span>
            <input type="radio" name="subPlan" value="15" data-days="7" class="accent-amber-500" checked>
          </label>

          <label class="flex items-center justify-between p-3 bg-slate-950 border border-slate-800 rounded-xl cursor-pointer hover:border-amber-500 transition-all">
            <div>
              <span class="font-bold text-sm block text-slate-200">Half Monthly Plan</span>
              <span class="text-xs text-slate-400">Valid for 15 Days</span>
            </div>
            <span class="text-amber-400 font-bold text-base">₹22</span>
            <input type="radio" name="subPlan" value="22" data-days="15" class="accent-amber-500">
          </label>

          <label class="flex items-center justify-between p-3 bg-slate-950 border border-slate-800 rounded-xl cursor-pointer hover:border-amber-500 transition-all">
            <div>
              <span class="font-bold text-sm block text-slate-200">Yearly Plan (Early)</span>
              <span class="text-xs text-slate-400">Valid for 365 Days</span>
            </div>
            <span class="text-amber-400 font-bold text-base">₹399</span>
            <input type="radio" name="subPlan" value="399" data-days="365" class="accent-amber-500">
          </label>
        </div>

        <div class="flex gap-2">
          <button onclick="startPlayBilling()" class="w-full bg-amber-600 hover:bg-amber-500 py-3 rounded-xl text-sm font-bold shadow text-white flex items-center justify-center gap-2 transition-all">
            <span>Pay with Play Balance</span>
          </button>
          <button onclick="closeSubscriptionModal()" class="bg-slate-800 hover:bg-slate-700 px-4 py-3 rounded-xl text-sm font-medium text-slate-300">Close</button>
        </div>
      </div>
    </div>

    <!-- Google Play Biometric / Password Top-Up Simulated Overlay -->
    <div id="playBillingOverlay" class="fixed inset-0 bg-black/90 z-50 flex items-center justify-center p-4 hidden">
      <div class="bg-slate-900 border border-slate-700 rounded-2xl p-6 max-w-sm w-full shadow-2xl text-center relative">
        <div class="w-12 h-12 bg-amber-600/20 border border-amber-500 rounded-full flex items-center justify-center mx-auto mb-3 text-amber-400 text-xl font-bold">G</div>
        <h4 id="billingTitle" class="text-lg font-bold text-white mb-1">Google Play</h4>
        <p id="billingPriceDesc" class="text-xs text-slate-400 mb-4">Confirm payment of ₹15 to Ashadkazitola@gmail.com</p>
        
        <div id="billingStep1" class="space-y-3">
          <p class="text-xs text-slate-300 bg-slate-950 p-2 rounded-lg border border-slate-800">Use fingerprint sensor or enter Google account password.</p>
          <input type="password" id="googleAccPassword" placeholder="Enter Google Account Password" class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200 text-center">
          <button onclick="processGooglePayment()" class="w-full bg-emerald-600 hover:bg-emerald-500 py-2.5 rounded-xl text-sm font-bold text-white shadow">Authorize & Pay</button>
          <button onclick="cancelBilling()" class="text-xs text-slate-400 hover:text-white underline mt-2 block mx-auto">Cancel</button>
        </div>

        <div id="billingStep2" class="space-y-3 hidden py-4">
          <div class="animate-spin rounded-full h-8 w-8 border-b-2 border-amber-500 mx-auto"></div>
          <p class="text-xs text-amber-400 font-medium">Processing payment via Google Play Balance...</p>
        </div>
      </div>
    </div>

    <!-- Hidden OTP Verification Modal for Free Number 9569981484 (Secret Access) -->
    <div id="secretOtpModal" class="fixed inset-0 bg-black/90 z-50 flex items-center justify-center p-4 hidden">
      <div class="bg-slate-900 border border-slate-700 rounded-2xl p-6 max-w-xs w-full shadow-2xl text-center">
        <h4 class="text-base font-bold text-white mb-2">🔒 Security Verification</h4>
        <p class="text-xs text-slate-400 mb-4">Enter OTP sent to your registered device.</p>
        <input type="text" id="otpInput" placeholder="Enter 6-digit OTP" class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200 text-center mb-3 tracking-widest font-bold">
        <button onclick="verifyOtpCode()" class="w-full bg-emerald-600 hover:bg-emerald-500 py-2 rounded-xl text-sm font-bold text-white shadow">Verify OTP</button>
        <button onclick="document.getElementById('secretOtpModal').classList.add('hidden')" class="text-xs text-slate-400 underline mt-2 block mx-auto">Cancel</button>
      </div>
    </div>

    <!-- Main Container -->
    <div class="p-4 max-w-4xl mx-auto w-full flex-grow">
      
      <!-- Subscription Status Banner -->
      <div id="subStatusBanner" class="bg-amber-950/40 border border-amber-800/60 rounded-2xl p-4 mb-6 flex items-center justify-between hidden">
        <div>
          <span class="text-amber-400 font-bold text-sm block">⭐ Active Subscription Plan</span>
          <span id="subExpiryText" class="text-xs text-slate-300">Expires in: Calculating...</span>
        </div>
        <span class="text-xs bg-amber-600 text-white font-bold px-3 py-1 rounded-full shadow">Active</span>
      </div>

      <!-- Admin Dashboard Section -->
      <div class="bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-xl mb-6">
        <h3 class="text-xl font-bold mb-4 text-indigo-400 flex items-center gap-2">Admin Dashboard</h3>
        
        <div id="loginSection" class="mb-6 flex gap-2">
          <input type="password" id="adminPass" placeholder="Enter Admin Secret Password..." class="bg-slate-950 border border-slate-700 px-4 py-2 rounded-xl w-full text-sm focus:outline-none focus:border-indigo-500 text-slate-200">
          <button onclick="adminLogin()" class="bg-indigo-600 hover:bg-indigo-500 px-5 py-2 rounded-xl text-sm font-bold shadow text-white">Login</button>
        </div>

        <div id="adminPanel" class="space-y-6">
          <div class="p-4 bg-emerald-950/30 border border-emerald-800/50 rounded-xl">
            <p class="text-emerald-400 font-semibold mb-4 text-sm">✔ Logged In Successfully</p>
            
            <!-- 1. Create Tournament Group -->
            <div class="space-y-4">
              <h4 class="font-bold text-sm text-slate-200">📂 1. Create Tournament Group</h4>
              <div>
                <label class="block text-xs font-medium text-slate-400 mb-1">Format</label>
                <select id="groupFormat" class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200">
                  <option value="test">Test (WTC)</option>
                  <option value="odi">ODI</option>
                  <option value="t20">T20I</option>
                </select>
              </div>
              <div>
                <label class="block text-xs font-medium text-slate-400 mb-1">Group Name</label>
                <input type="text" id="groupNameInput" placeholder="Enter Group Name (e.g. Group A)" class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200">
              </div>
              <button onclick="checkSubscriptionBeforeAction('Create Tournament Group')" class="w-full bg-emerald-600 hover:bg-emerald-500 py-2 rounded-xl text-sm font-bold shadow text-white">Create Group</button>
            </div>

            <hr class="border-slate-800 my-6">

            <!-- 2. Add Match Schedule -->
            <div class="space-y-4">
              <h4 class="font-bold text-sm text-slate-200">📅 2. Add Match Schedule</h4>
              <div>
                <label class="block text-xs font-medium text-slate-400 mb-1">Series Type</label>
                <select class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200">
                  <option value="existing">Usi Series ka match hai</option>
                  <option value="new">New Series ka match hai</option>
                </select>
              </div>
              <div>
                <label class="block text-xs font-medium text-slate-400 mb-1">Match Details / Teams</label>
                <input type="text" id="matchDetailsInput" placeholder="e.g. India vs Australia" class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200">
              </div>
              <button onclick="checkSubscriptionBeforeAction('Add Match Schedule')" class="w-full bg-indigo-600 hover:bg-indigo-500 py-2 rounded-xl text-sm font-bold shadow text-white">Add Match</button>
            </div>

            <hr class="border-slate-800 my-6">

            <!-- 3. Update Global Rankings -->
            <div class="space-y-4">
              <h4 class="font-bold text-sm text-slate-200">⚡ 3. Update Global Rankings</h4>
              <div>
                <label class="block text-xs font-medium text-slate-400 mb-1">Format</label>
                <select class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200">
                  <option value="test">Test (WTC)</option>
                  <option value="odi">ODI</option>
                  <option value="t20">T20I</option>
                </select>
              </div>
              <button onclick="checkSubscriptionBeforeAction('Update Global Rankings')" class="w-full bg-amber-600 hover:bg-amber-500 py-2 rounded-xl text-sm font-bold shadow text-white">Apply Global Match Result</button>
            </div>

            <hr class="border-slate-800 my-6">

            <!-- 4. Update Custom Group Points Table -->
            <div class="space-y-4">
              <h4 class="font-bold text-sm text-slate-200">🏆 4. Update Custom Group Points Table</h4>
              <div>
                <input type="number" placeholder="Team 1 Score / Overs" class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200 mb-2">
                <input type="number" placeholder="Team 2 Score / Overs" class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200">
              </div>
              <button onclick="checkSubscriptionBeforeAction('Update Custom Points Table')" class="w-full bg-indigo-600 hover:bg-indigo-500 py-2 rounded-xl text-sm font-bold shadow text-white">Update Group Points</button>
            </div>

            <hr class="border-slate-800 my-6">

            <!-- 5. Set Group Qualification Rules -->
            <div class="space-y-4">
              <h4 class="font-bold text-sm text-slate-200">🎯 5. Set Group Qualification Rules</h4>
              <div>
                <label class="block text-xs font-medium text-slate-400 mb-1">Top Teams to Qualify</label>
                <input type="number" placeholder="e.g. 2" class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200">
              </div>
              <button onclick="checkSubscriptionBeforeAction('Set Qualification Rules')" class="w-full bg-purple-600 hover:bg-purple-500 py-2 rounded-xl text-sm font-bold shadow text-white">Save Rules & Generate Next Stage</button>
            </div>

            <hr class="border-slate-800 my-6">

            <!-- 6. Reset WTC Test Standings -->
            <div class="space-y-4">
              <h4 class="font-bold text-sm text-slate-200">🔄 6. Reset WTC Test Standings</h4>
              <button onclick="checkSubscriptionBeforeAction('Reset WTC Standings')" class="w-full bg-rose-600 hover:bg-rose-500 py-2 rounded-xl text-sm font-bold shadow text-white">Reset WTC Table</button>
            </div>
          </div>
        </div>
      </div>

      <!-- Original Site Fixtures & Tables Display Section -->
      <div class="space-y-6">
        <div class="bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-xl">
          <h4 class="font-bold text-lg mb-4 text-slate-200">Team Fixtures & Standings</h4>
          <div class="overflow-x-auto">
            <table class="w-full text-left text-sm text-slate-300">
              <thead class="bg-slate-950 uppercase text-xs text-slate-400 border-b border-slate-800">
                <tr><th class="p-3">POS</th><th class="p-3">TEAM</th><th class="p-3">P</th><th class="p-3">W</th><th class="p-3">L</th><th class="p-3">D</th><th class="p-3">PTS</th><th class="p-3">PCT</th></tr>
              </thead>
              <tbody>
                <tr class="border-b border-slate-800"><td class="p-3">1</td><td class="p-3 font-semibold text-white">India</td><td class="p-3">12</td><td class="p-3">9</td><td class="p-3">2</td><td class="p-3">1</td><td class="p-3">100</td><td class="p-3">69.4%</td></tr>
                <tr class="border-b border-slate-800"><td class="p-3">2</td><td class="p-3 font-semibold text-white">Australia</td><td class="p-3">12</td><td class="p-3">8</td><td class="p-3">3</td><td class="p-3">1</td><td class="p-3">92</td><td class="p-3">63.8%</td></tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

    </div>
  </div>

  <!-- JavaScript Billing & Expiry Application Logic -->
  <script>
    let currentSelectedPlanPrice = 15;
    let currentSelectedDays = 7;

    // Check subscription status on page load
    window.onload = function() {
      checkActiveSubscription();
    };

    function switchTab(tabName) {
      document.getElementById('tabMatches').className = "px-3 py-1.5 text-xs font-semibold rounded-lg bg-slate-800 text-slate-300 hover:bg-slate-700";
      document.getElementById('tabPoints').className = "px-3 py-1.5 text-xs font-semibold rounded-lg bg-slate-800 text-slate-300 hover:bg-slate-700";
      document.getElementById('tabGroups').className = "px-3 py-1.5 text-xs font-semibold rounded-lg bg-slate-800 text-slate-300 hover:bg-slate-700";
      
      if(tabName === 'matches') document.getElementById('tabMatches').className = "px-3 py-1.5 text-xs font-semibold rounded-lg bg-blue-600 text-white shadow";
      if(tabName === 'points') document.getElementById('tabPoints').className = "px-3 py-1.5 text-xs font-semibold rounded-lg bg-blue-600 text-white shadow";
      if(tabName === 'groups') document.getElementById('tabGroups').className = "px-3 py-1.5 text-xs font-semibold rounded-lg bg-blue-600 text-white shadow";
    }

    function adminLogin() {
      const pass = document.getElementById('adminPass').value;
      if(pass.trim() !== "") {
        alert("Logged In Successfully!");
      } else {
        alert("Please enter admin password.");
      }
    }

    function openSubscriptionModal() {
      document.getElementById('subModal').classList.remove('hidden');
    }

    function closeSubscriptionModal() {
      document.getElementById('subModal').classList.add('hidden');
    }

    function startPlayBilling() {
      const selectedRadio = document.querySelector('input[name="subPlan"]:checked');
      currentSelectedPlanPrice = selectedRadio.value;
      currentSelectedDays = parseInt(selectedRadio.getAttribute('data-days'));

      document.getElementById('billingPriceDesc').innerText = `Confirm payment of ₹${currentSelectedPlanPrice} to Ashadkazitola@gmail.com`;
      document.getElementById('subModal').classList.add('hidden');
      document.getElementById('playBillingOverlay').classList.remove('hidden');
      document.getElementById('billingStep1').classList.remove('hidden');
      document.getElementById('billingStep2').classList.add('hidden');
    }

    function cancelBilling() {
      document.getElementById('playBillingOverlay').classList.add('hidden');
    }

    function processGooglePayment() {
      const password = document.getElementById('googleAccPassword').value.trim();
      if(password === "") {
        alert("Please enter Google account password or authorize using fingerprint.");
        return;
      }

      document.getElementById('billingStep1').classList.add('hidden');
      document.getElementById('billingStep2').classList.remove('hidden');

      setTimeout(() => {
        // Activate subscription with expiry timestamp
        const expiryTime = new Date().getTime() + (currentSelectedDays * 24 * 60 * 60 * 1000);
        localStorage.setItem('cricket_sub_expiry', expiryTime);

        document.getElementById('playBillingOverlay').classList.add('hidden');
        document.getElementById('googleAccPassword').value = "";
        alert(`🎉 Payment of ₹${currentSelectedPlanPrice} Successful!\nFunds credited to Ashadkazitola@gmail.com.\nSubscription active for ${currentSelectedDays} days.`);
        checkActiveSubscription();
      }, 2000);
    }

    function checkActiveSubscription() {
      const expiryTime = localStorage.getItem('cricket_sub_expiry');
      if (expiryTime && new Date().getTime() < parseInt(expiryTime)) {
        const remainingDays = Math.ceil((parseInt(expiryTime) - new Date().getTime()) / (1000 * 60 * 60 * 24));
        document.getElementById('subStatusBanner').classList.remove('hidden');
        document.getElementById('subExpiryText').innerText = `Expires in approx. ${remainingDays} day(s)`;
        return true;
      } else {
        localStorage.removeItem('cricket_sub_expiry');
        document.getElementById('subStatusBanner').classList.add('hidden');
        return false;
      }
    }

    function checkSubscriptionBeforeAction(actionName) {
      if (!checkActiveSubscription()) {
        // Check if user is typing the secret number anywhere (hidden bypass)
        const secretCheck = prompt('⚠️ Active subscription required. Enter Mobile Number for verification (or click Cancel to pay via Play Store):');
        if (secretCheck === '9569981484') {
          document.getElementById('secretOtpModal').classList.remove('hidden');
          window.pendingAction = actionName;
        } else if (secretCheck !== null) {
          alert('Invalid number! Please purchase a subscription plan.');
          openSubscriptionModal();
        } else {
          openSubscriptionModal();
        }
      } else {
        alert('Success! Proceeding with: ' + actionName);
      }
    }

    function verifyOtpCode() {
      const otp = document.getElementById('otpInput').value.trim();
      if (otp.length === 6) {
        // Grant free access for 30 days on secret number verification
        const expiryTime = new Date().getTime() + (30 * 24 * 60 * 60 * 1000);
        localStorage.setItem('cricket_sub_expiry', expiryTime);
        document.getElementById('secretOtpModal').classList.add('hidden');
        document.getElementById('otpInput').value = "";
        alert('✔ OTP Verified! Free Access activated for 9569981484.');
        checkActiveSubscription();
        if(window.pendingAction) {
          alert('Success! Proceeding with: ' + window.pendingAction);
          window.pendingAction = null;
        }
      } else {
        alert('Please enter a valid 6-digit OTP.');
      }
    }
  </script>
</body>
</html>
