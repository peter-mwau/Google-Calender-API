# Google-Calender-API
It's a small simple project that leverages  on the Google Calendar API to come up with interactive  calendars and reminders that can be integrated in Python projects

## Prerequisites
To run this quickstart, you need the following prerequisites:

Python 3.10.7 or greater
The pip package management tool
A Google Cloud project.
A Google account with Google Calendar enabled

## Set up your environment
In the Google Cloud console, enable the Google Calendar API


## Configure the OAuth consent screen
In the Google Cloud console, go to Menu menu > APIs & Services > OAuth consent screen.
Go to the OAuth consent screen

Select the user type for your app, then click Create.
Complete the app registration form, then click Save and Continue.
For now, you can skip adding scopes and click Save and Continue. In the future, when you create an app for use outside of your Google Workspace organization, you must add and verify the authorization scopes that your app requires.

If you selected External for user type, add test users:
Under Test users, click Add users.
Enter your email address and any other authorized test users, then click Save and Continue.
Review your app registration summary. To make changes, click Edit. If the app registration looks OK, click Back to Dashboard.

## Authorize credentials for a desktop application
In the Google Cloud console, go to Menu menu > APIs & Services > Credentials.
Go to Credentials

Click Create Credentials > OAuth client ID.
Click Application type > Desktop app.
In the Name field, type a name for the credential. This name is only shown in the Google Cloud console.
Click Create. The OAuth client-created screen appears, showing your new Client ID and Client secret.
Click OK. The newly created credential appears under OAuth 2.0 Client IDs.
Save the downloaded JSON file as ### credentials.json ###, and move the file to your working directory.

## Install the Google client library
Install the Google client library for Python:

```
pip install --upgrade google-api-python-client google-auth-httplib2 google-auth-oauthlib
```
## Configure the sample
```
from __future__ import print_function

import datetime
import os.path

from google.auth.transport.requests import Request
from google.oauth2.credentials import Credentials
from google_auth_oauthlib.flow import InstalledAppFlow
from googleapiclient.discovery import build
from googleapiclient.errors import HttpError

# If modifying these scopes, delete the file token.json.
SCOPES = ['https://www.googleapis.com/auth/calendar.readonly']


def main():
    """Shows basic usage of the Google Calendar API.
    Prints the start and name of the next 10 events on the user's calendar.
    """
    creds = None
    # The file token.json stores the user's access and refresh tokens, and is
    # created automatically when the authorization flow completes for the first
    # time.
    if os.path.exists('token.json'):
        creds = Credentials.from_authorized_user_file('token.json', SCOPES)
    # If there are no (valid) credentials available, let the user log in.
    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            flow = InstalledAppFlow.from_client_secrets_file(
                'credentials.json', SCOPES)
            creds = flow.run_local_server(port=0)
        # Save the credentials for the next run
        with open('token.json', 'w') as token:
            token.write(creds.to_json())

    try:
        service = build('calendar', 'v3', credentials=creds)

        # Call the Calendar API
        now = datetime.datetime.utcnow().isoformat() + 'Z'  # 'Z' indicates UTC time
        print('Getting the upcoming 10 events')
        events_result = service.events().list(calendarId='primary', timeMin=now,
                                              maxResults=10, singleEvents=True,
                                              orderBy='startTime').execute()
        events = events_result.get('items', [])

        if not events:
            print('No upcoming events found.')
            return

        # Prints the start and name of the next 10 events
        for event in events:
            start = event['start'].get('dateTime', event['start'].get('date'))
            print(start, event['summary'])

    except HttpError as error:
        print('An error occurred: %s' % error)


if __name__ == '__main__':
    main()
```
## Run the sample
```
python3 quickstart.py
```
