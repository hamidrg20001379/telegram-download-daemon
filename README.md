# telegram-download-daemon

این پروژه یک «دانلودر خودکار تلگرام» (Daemon، نه Bot) است که با اکانت واقعی تلگرام شما کار می‌کند و می‌تواند فایل‌های بزرگ‌تر از محدودیت ربات‌ها را دانلود کند.

> مناسب برای: کاربرانی که می‌خواهند فایل‌های کانال/چت را به‌صورت خودکار دانلود کنند.
>
> این پروژه برای دانلود قانونی و محتوایی که مجوز آن را دارید طراحی شده است.

## نسخه ساده برای کاربران فارسی‌زبان (بدون نیاز به برنامه‌نویسی)

اگر با کدنویسی آشنا نیستید، راحت‌ترین مسیر برای شروع:

1. فایل نوت‌بوک آماده را باز کنید: `notebooks/telegram_to_gdrive_colab.ipynb` (این نوت‌بوک خودش پروژه را از GitHub دریافت و نصب می‌کند)
2. آن را در **Google Colab** اجرا کنید.
3. با وارد کردن اطلاعات اکانت تلگرام خود (API ID / API HASH / شماره تلفن)، وارد تلگرام شوید.
4. فایل را در تلگرام (مثلاً Saved Messages) ارسال/فوروارد کنید؛ بدون نیاز به `message_id`.
5. فایل دانلودشده مستقیم داخل **Google Drive** شما ذخیره می‌شود.

راهنمای کامل فارسی را در این فایل بخوانید:

- [`docs/PERSIAN_GUIDE.md`](docs/PERSIAN_GUIDE.md)

---

## What this project does

A Telegram Daemon (not a bot) for file downloading automation [for channels of which you have admin privileges](https://github.com/alfem/telegram-download-daemon/issues/48).

If you have got an Internet connected computer or NAS and you want to automate file downloading from Telegram channels, this daemon is for you.

Telegram bots are limited to 20Mb file size downloads. So this daemon allows bigger downloads (limited by Telegram APIs and Telethon support).

## Installation

You need Python3 (3.6 works fine, 3.5 may crash randomly).

Install dependencies by running this command:

```bash
pip install -r requirements.txt
```

(If you don't want to install `cryptg` and its dependencies, you just need to install `telethon`.)

Warning: If you get a `File size too large` message, check the version of Telethon library you are using. Old versions have a lower file-size limit.

Obtain your own API ID/API HASH: https://core.telegram.org/api/obtaining_api_id

## Usage

You need to configure these values:

| Environment Variable     | Command Line argument | Description                                                  | Default Value       |
|--------------------------|:---------------------:|--------------------------------------------------------------|---------------------|
| `TELEGRAM_DAEMON_API_ID`   | `--api-id`              | api_id from https://core.telegram.org/api/obtaining_api_id   |                     |
| `TELEGRAM_DAEMON_API_HASH` | `--api-hash`            | api_hash from https://core.telegram.org/api/obtaining_api_id |                     |
| `TELEGRAM_DAEMON_DEST`     | `--dest`                | Destination path for downloaded files                        | `/telegram-downloads` |
| `TELEGRAM_DAEMON_TEMP`     | `--temp`                | Destination path for temporary (in-progress) files           | use --dest |
| `TELEGRAM_DAEMON_CHANNEL`  | `--channel`             | Channel id to download from it (check [Issue 45](https://github.com/alfem/telegram-download-daemon/issues/45), [Issue 48](https://github.com/alfem/telegram-download-daemon/issues/48), [Issue 73](https://github.com/alfem/telegram-download-daemon/issues/73)) | |
| `TELEGRAM_DAEMON_DUPLICATES`  | `--duplicates`             | What to do with duplicated files: ignore, overwrite or rename | rename |
| `TELEGRAM_DAEMON_WORKERS`  | `--workers`             | Number of simultaneous downloads | Equals to processor cores |

You can define them as environment variables, or put them as command-line arguments, for example:

```bash
python telegram-download-daemon.py --api-id <your-id> --api-hash <your-hash> --channel <channel-number>
```

Finally, resend any file link to the channel to start the downloading. This daemon can manage many downloads simultaneously.

You can also 'talk' to this daemon using your Telegram client:

- Say `list` and get a list of available files in the destination path.
- Say `status` to check current status.
- Say `clean` to remove stale (`*.tdd`) files from temporary directory.
- Say `queue` to list pending files waiting to start.

## Docker

```bash
docker pull alfem/telegram-download-daemon
```

When using [`TelegramClient`](https://docs.telethon.dev/en/latest/quick-references/client-reference.html#telegramclient), the first login requires interactive console input (phone number + confirmation code).

For Docker, run interactively for first-time authentication:

```bash
docker-compose run --rm telegram-download-daemon
# Interact with the console to authenticate.
# Wait for: "Signed in successfully as ..."
# Then close the container

docker-compose up -d
```

See the `sessions` volume in [`docker-compose.yml`](docker-compose.yml).
