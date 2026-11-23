# NodeSeek-Signin

English · [简体中文](README.md)

<div align="center">
  
![NodeSeek](https://img.shields.io/badge/NodeSeek-自动签到-green)
![GitHub stars](https://img.shields.io/github/stars/yowiv/NodeSeek-Signin?style=flat)
![Python](https://img.shields.io/badge/Language-Python-blue)
![License](https://img.shields.io/github/license/yowiv/NodeSeek-Signin)

</div>

[Deepflood forum check-in](https://github.com/yowiv/deepflood-Signin)


## 📝 About

An automatic check-in tool for the NodeSeek forum. It supports scheduled automatic check-ins via GitHub Actions, QingLong panel, or Docker Compose. Check-ins are random by default, so you can easily collect the forum's daily "chicken leg" reward.


## ✨ Features

- 📅 Automatic runs with GitHub Actions
- 🦉 Scheduled tasks on the QingLong panel
- 🐳 One-click deployment with Docker Compose
- 🍪 Login with a Cookie or with username and password
- 👥 Batch check-in for multiple accounts
- 🔐 Multiple captcha solutions
  - Self-hosted CloudFreed service (free)
  - YesCaptcha commercial service (paid / gifted credits)
- 📱 Multiple notification channels (add the corresponding variables in blank.yml)
- 🔄 Automatically refreshes the Cookie and saves it to a GitHub variable

## 🚀 Usage

### Option 1: GitHub Actions

1. Fork this repository to your own GitHub account
2. In the repository's `Settings > Secrets and variables > Actions`, add the required configuration:

| Variable | Required | Description |
| :------: | :----: | :--- |
| `NS_COOKIE` | **Recommended** | Your NodeSeek forum user Cookie, available from the network requests in your browser's developer tools (F12). Separate multiple accounts with `&` or a newline |
| `USER`/`USER1` | Optional | NodeSeek forum username, used when the Cookie expires |
| `PASS`/`PASS1` | Optional | NodeSeek forum password |
| `TG_BOT_TOKEN` | Optional | Telegram bot token, used to send check-in results |
| `TG_USER_ID` | Optional | Telegram user ID that receives the notifications |
| `TG_THREAD_ID` | Optional | Telegram supergroup topic ID, for sending notifications to a specific topic |
| `GH_PAT` | Optional | GitHub Personal Access Token, used to automatically update the Cookie variable |

> **Note**: If you only set a Cookie without configuring a captcha service, the script cannot log in again to fetch a new Cookie once the old one expires.

### Option 2: QingLong panel

Run the following command in the QingLong panel to clone this repository:

```bash
ql repo https://github.com/yowiv/NodeSeek-Signin.git
```

Then add the required configuration in environment variables.

### Option 3: Docker Compose

This deployment method enables fully local, scheduled automatic check-ins, including automatic captcha handling.

**Step 1: Clone the project**

First, clone the whole project to your server:

```bash
git clone https://github.com/yowiv/NodeSeek-Signin.git
cd NodeSeek-Signin
```

**Step 2: Configure environment variables**

Copy or rename the `.env.example` file to `.env`, then edit the `.env` file and fill in your configuration.

```bash
cp .env.example .env
```

Fill in the following key settings, following the comments in the file:
- **Account info**: `USER1`, `PASS1`, `USER2`, `PASS2`, etc.
- **Captcha service**: A captcha service must be configured when logging in with a username and password. `yescaptcha` is recommended; fill in `CLIENTT_KEY`.
- **Scheduled task**: The `RUN_AT` variable sets when the check-in runs.
    - **Fixed time**: e.g. `10:30` means run every day at 10:30 AM.
    - **Time range**: e.g. `10:00-18:00` means pick a random time between 10:00 AM and 6:00 PM every day.
    - **Default**: If unset, defaults to `09:00-21:00`.

**Step 3: Start the service**

In the directory containing `docker-compose.yml` and `.env`, run the following command to build and start the service in the background:

```bash
docker-compose up -d
```

**Step 4: Check the logs**

You can watch the container logs in real time with the following command to confirm the service is running and check-ins are succeeding:

```bash
docker-compose logs -f
```

**Step 5: Stop the service**

To stop and remove the service container, run:

```bash
docker-compose down
```

### Option 4: Username/password login (fetches a new Cookie automatically)

When the Cookie expires, the script tries logging in with the username and password to get a new Cookie. Login requires captcha verification, with two supported solutions:

#### Solution A: Self-hosted CloudFreed service (recommended for home-broadband users)

```bash
docker run -itd   --name cloudflyer   -p 3000:3000   --restart unless-stopped   jackzzs/cloudflyer -K your-client-key -H 0.0.0.0
```

Configure the following environment variables:

| Variable | Description |
| :------: | :--- |
| `API_BASE_URL` | CloudFreed service address, e.g. `http://192.168.1.100:3000` |
| `CLIENTT_KEY` | CloudFreed service client key |
| `USER1`/`USER2`... | NodeSeek forum username |
| `PASS1`/`PASS2`... | NodeSeek forum password |
| `SOLVER_TYPE` | Set to `turnstile` (default) |

#### Solution B: YesCaptcha commercial service (recommended if you cannot self-host)

1. Register an account at [YesCaptcha](https://yescaptcha.com/i/k2Hy3Q)
2. After registering, contact support to receive free credits (roughly 60 logins)
3. Configure the following environment variables:

| Variable | Description |
| :------: | :--- |
| `CLIENTT_KEY` | YesCaptcha API key |
| `USER1`/`USER2`... | NodeSeek forum username |
| `PASS1`/`PASS2`... | NodeSeek forum password |
| `SOLVER_TYPE` | Set to `yescaptcha` |

> **Tip**: YesCaptcha provides two endpoints — pick one based on your network:
> - International endpoint: `https://api.yescaptcha.com` (default)
> - China endpoint: `https://cn.yescaptcha.com`

### Multi-account setup

This script supports check-ins for multiple accounts, configured as follows:

#### 1. Cookie method

Set multiple accounts in the `NS_COOKIE` environment variable, separated by `&` or newlines:

```
# Separate multiple Cookies with &
NS_COOKIE=Cookie1&Cookie2&Cookie3

# Or separate them with newlines
NS_COOKIE=Cookie1
Cookie2
Cookie3
```

#### 2. Username/password method

Set up multiple accounts with numbered variables:

```
# First account
USER1=username1
PASS1=password1

# Second account
USER2=username2
PASS2=password2

# Third account
USER3=username3
PASS3=password3

# And so on...
```

> **Note**: The plain `USER` and `PASS` variables are also recognized. The script automatically detects all configured accounts and runs the check-in for each in turn.

## 🔑 GitHub Personal Access Token setup

To automatically refresh the Cookie, the script needs a GitHub Personal Access Token (PAT) so it can save the new Cookie to a repository variable.

### 1. Create a Personal Access Token

1. Log in to your GitHub account
2. Click your avatar in the top-right corner → choose "Settings"
3. Scroll to the bottom of the page → click "Developer settings"
4. In the left menu, choose "Personal access tokens" → click "Tokens (classic)"
5. Click "Generate new token" → choose "Generate new token (classic)"
6. Configure the token:
   - Name: a descriptive name, e.g. "NodeSeek check-in script"
   - Expiration: choose as needed (90 days or longer is recommended)
   - Check these permissions:
     - `repo` (full repository access)
     - `workflow` (for managing GitHub Actions)
7. Click the "Generate token" button at the bottom of the page
8. **Copy the generated token immediately** — you will not be able to see it again after leaving the page

### 2. Add it to the repository Secrets

1. Open your NodeSeek-Signin repository
2. Click "Settings" → "Secrets and variables" → "Actions"
3. Click "New repository secret"
4. Name: `GH_PAT`
5. Value: the Personal Access Token you just copied
6. Click "Add secret" to save

Once this is set up, the check-in script can automatically save a valid Cookie to a GitHub repository variable, so the next run can use it directly — fewer repeat logins and captcha challenges.

## 🔧 Full environment variable reference

| Variable | Required | Default | Description |
| :------: | :----: | :----: | :--- |
| `NS_COOKIE` | Recommended | - | NodeSeek forum user Cookie; separate multiple accounts with `&` or newlines |
| `USER1`, `USER2`... | Optional | - | NodeSeek forum username, used when the Cookie expires |
| `PASS1`, `PASS2`... | Optional | - | NodeSeek forum password |
| `NS_RANDOM` | Optional | true | Whether to check in at a random time (true/false) |
| `RUN_AT` | Optional | `09:00-21:00` | **Docker Compose only**. Sets when the scheduled task runs; supports a fixed time `10:30` or a time range `10:00-18:00` |
| `SOLVER_TYPE` | Optional | turnstile | Captcha solution (turnstile/yescaptcha) |
| `API_BASE_URL` | Conditionally required | - | CloudFreed service address, required when SOLVER_TYPE=turnstile |
| `CLIENTT_KEY` | Required | - | Captcha service client key |
| `GH_PAT` | Optional | - | GitHub Personal Access Token, used to automatically update the Cookie variable |
| Notification variables | Optional | - | Configuration for various push notification platforms |

## 📊 Captcha service comparison

| Service | Type | Pros | Cons | Recommendation |
| :--: | :--: | :--- | :--- | :------: |
| CloudFreed | Self-hosted | Free, no usage limit | You deploy and maintain it yourself | ★★★★☆ |
| YesCaptcha | Commercial | Stable, reliable, easy to configure | Paid service (has a free allowance) | ★★★★★ |

## ⚠️ Disclaimer

This project is for learning and exchange only. Please follow the NodeSeek forum's rules and terms.

## 👨‍💻 Local development and testing

For local development and debugging, the project provides a non-intrusive test setup. Follow the steps below to run the check-in script locally.

### Step 1: Create a virtual environment

Creating a separate Python virtual environment is recommended to avoid dependency conflicts.

```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate
```

### Step 2: Install dependencies

The project has two dependency files: `requirements.txt` for production, and `requirements-dev.txt` with extra libraries needed for local testing.

```bash
pip install -r requirements.txt
pip install -r requirements-dev.txt
```

### Step 3: Configure environment variables

Copy the environment variable example file `.env.example` to `.env`, then fill in your test configuration, such as account credentials and captcha service keys.

```bash
cp .env.example .env
```

### Step 4: Run the test

Once configured, run the following command for a one-off check-in test.

```bash
python test_run.py
```

The script automatically loads the environment variables from the `.env` file and runs the main check-in program, so you can quickly verify the check-in logic locally.
