# X-unfollow-script

🧹 X (Twitter) Smart Unfollow Script

A lightweight browser console script that helps you clean up your X (Twitter) following list by unfollowing accounts safely and selectively.

It only unfollows accounts that:

❌ Do NOT follow you back
❌ Have fewer than a configurable follower threshold (default: 7,000)
⚙️ Features
🧠 Smart filtering (skips mutual followers)
👥 Follower count whitelist (e.g. 7,000+ followers = protected)
🚦 Rate-limited to reduce risk
📉 Configurable unfollow limit per session
🪶 Runs directly in browser console (no installs needed)
🚀 How It Works

The script:

Scans your Following page
Checks each account:
If they follow you
Their follower count
Unfollows only eligible accounts
Stops when limit is reached
Logs progress in real-time
📦 Setup Instructions
1️⃣ Open X (Twitter) in your browser

Go to:

https://x.com

Login to your account.

2️⃣ Go to your Following page
https://x.com/your_username/following
3️⃣ Open Developer Console
Windows/Linux: Ctrl + Shift + J
Mac: Cmd + Option + J

Click on the Console tab

4️⃣ Paste the script

(async () => {
  const UNFOLLOW_LIMIT = 100;      // 🔴 recommended: 50–150
  const FOLLOWER_WHITELIST = 7000; // 🔒 auto-whitelist
  let unfollowedCount = 0;

  const sleep = (ms) => new Promise(r => setTimeout(r, ms));
  const scrollToBottom = () => window.scrollTo(0, document.body.scrollHeight);

  const getUserCards = () =>
    Array.from(document.querySelectorAll('[data-testid="UserCell"]'));

  const followsYou = (card) =>
    card.innerText.includes("Follows you");

  const getFollowerCount = (card) => {
    const match = card.innerText.match(/([\d,.]+)\sFollowers/i);
    if (!match) return 0;

    let count = match[1].replace(/,/g, '');

    if (count.includes('.')) {
      count = parseFloat(count) * 1000;
    }

    return Number(count);
  };

  const unfollow = async (card) => {
    const unfollowBtn = card.querySelector('[data-testid$="-unfollow"]');
    if (!unfollowBtn) return;

    unfollowBtn.click();
    await sleep(1500);

    const confirmBtn = document.querySelector('[data-testid="confirmationSheetConfirm"]');
    confirmBtn && confirmBtn.click();

    unfollowedCount++;
    console.log(`✅ Unfollowed (${unfollowedCount}/${UNFOLLOW_LIMIT})`);
    await sleep(3500); // safety delay
  };

  while (unfollowedCount < UNFOLLOW_LIMIT) {
    scrollToBottom();
    await sleep(2500);

    const cards = getUserCards();

    for (const card of cards) {
      if (unfollowedCount >= UNFOLLOW_LIMIT) break;

      const followerCount = getFollowerCount(card);

      if (
        !followsYou(card) &&
        followerCount < FOLLOWER_WHITELIST
      ) {
        await unfollow(card);
      } else {
        console.log(
          `⏭️ Skipped — ${
            followsYou(card) ? 'Follows you' : `${followerCount}+ followers`
          }`
        );
      }
    }
  }

  console.log(`🚀 DONE — ${unfollowedCount} accounts unfollowed safely`);
})();


Copy and paste the full script into the console and press:

Enter
🧠 Configuration

You can adjust these values inside the script:

const UNFOLLOW_LIMIT = 100;

👉 Max number of accounts to unfollow per run
Recommended: 50–150

const FOLLOWER_WHITELIST = 7000;

👉 Accounts with 7,000+ followers will NOT be unfollowed

⏱️ Safety Mechanism

The script includes built-in delays:

1.5s pause between actions
3.5s cooldown after each unfollow
2.5s pause between scroll batches

This is designed to reduce risk of:

Rate limits
Temporary action blocks
Spam detection flags
🛑 How to Stop the Script

You can stop anytime by:

Refreshing the page, OR
Closing the tab
⚠️ Important Disclaimer

This script is provided for educational and personal use only.

By using this script, you acknowledge that:

You are responsible for any actions taken on your account
Excessive automation may violate X (Twitter) platform rules
The author assumes no liability for account restrictions, suspensions, or bans
Use at your own risk
💡 Recommended Usage

To stay safe:

Run once per day
Limit to 50–100 unfollows per session
Avoid repeated rapid execution
Do not use alongside VPN/IP switching tools
🔧 Future Improvements (Optional Ideas)
Whitelist specific usernames
Export skipped accounts list
Add “only unfollow inactive accounts”
Dry-run mode (no actions, only logs)
Ratio-based filtering (followers/following logic)
