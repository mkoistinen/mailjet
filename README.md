# Installation instructions

## Step A. Create a "virtual environment" for Python

1. Open your Terminal.app application on your Mac
2. Type `cd ~`
3. Type `mkdir mailjet`
4. Type `cd mailjet`
5. Type `python3 -m venv .mailjet`
6. Type `source .mailjet/bin/activate` to activate the virtual environment

## Step B. Install dependencies

1. Type `pip3 install -U pip3 pip-tools` (without the backticks)
2. Enter "Y" if asked to confirm anything
3. Type `pip-compile`
4. Type `pip-sync`

## Step C: Create settings.py file

1. Go to mailjet.com and log in with your normal username and password.
2. Click "SMS" in the toolbar at the top
3. Click "Generate your access token" in the "How to send SMS?" box.
4. Provide a label. This is to help you identify tokens you might wish to 
   revoke in the future.
5. Click "Generate"
6. Copy the access token into your system clipboard
7. Type `echo "ACCESS_TOKEN = 'PASTE ACCESS TOKEN HERE'" >> settings.py`

   > **NOTE**: You **need** to include the single-quotes around your
   > access token.

8. Type `cat settings.py` to verify that your file looks something like this:

> ACCESS_TOKEN = 'abcdef0123456789abcdef0123456789'

## Step D: Run a test
1. First, type `python3 -m mailjet --help` to see all the options and review
   them. Note that a "sender" is required by the MailJet API and will be set
   to your computer login name (E.g, 'mkoistinen' in my case) or, if that is
   not available, sender will be set to your computer's hostname. You can
   override what is sent by providing it on the command line with the `-s`
   option. E.g.:

    > python3 -m mailjet -m "Testing MailJet" -r "+12125551212" -s "MKoistinen"

    > **NOTE**: Recipients may, or may not see the sender information, so do
    > not set this to anything you expect to be seen. Also, keep this very
    > short and without punctuation.

2. Type `python3 -m mailjet -m "Testing Mailjet" -r "+12125551212"`. Use your
   own telephone number for the `-r` argument. Do not expect anything to happen
   yet, because this was a "dry run".

3. To test using a bunch of recipients from an XLSX file, type:

    > python3 -m mailjet -m "Your message here" -f path_to_spreadsheet.xlsx

The program will review the message you provide for the `-m` argument. If the
message is too long, it will need to be shortened before the program will send
it. If you use non-SMS-friendly characters, your available message length will
be nearly 50% shorter, so, try to stick to basic symbols (not emojis or foreign
language characters). Even just one such character will require the whole
message to be shorter and/or be more expensive to send.

If you provide a XLSX file with the `-f` argument, the program will also
attempt to locate a column with the any of the following headings: SMS, CELL,
MOBILE, PHONE, TELEPHONE. The first one such column it finds will be used to
source all the recipient phone numbers. It will then attempt to convert these
telephone numbers into E.164 or "international" format it will complain if it
cannot. Any such issues will need to be fixed or the program will refuse to
send anything to anyone.

So far, this is still a dry run so nothing will be sent, but this is how you
can ensure that you're all set to go for real.

## Step E: Full test, including the actual message being sent

1. Use the up-arrow in your terminal to get the previously typed command, then
   add `--for-real` at the end. Your command should now look something like
   this:

    > python3 -m mailjet -m "Testing Mailjet" -r "+12125551212" --for-real

   or

    > python3 -m mailjet -m "Testing Mailjet" -f path_to_spreadsheet.xlsx --for-real

   The program should report any errors, but hopefully, you'll have already
   sorted out any potential issues during the dry-run stage.
