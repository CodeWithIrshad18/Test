This guide adapts the principles of integrating the Data Validation API with your specific service, focusing on generating an authenticated session and making API calls to your API endpoint.

Step 1: Generate authenticated session

To begin, you'll need to create an authenticated session via a service account to start calling your API.
Library required:
pip install google-auth google-auth-oauthlib
Download the Service Account File & place it in your system.
The code is to be adjusted with the path to the service account in your system:




Code Snippet:
from google.oauth2 import service_account
from google.auth.transport.requests import AuthorizedSession

url = "https://tsuisl-data-validation-271687565758.asia-south1.run.app"

creds = service_account.IDTokenCredentials.from_service_account_file(
       '<path to service account>', target_audience=url)

authed_session = AuthorizedSession(creds)
payload = {
"passkey": "<Pass key>",
"Doc_string":"<Doc String>",
"Doc_ext": "pdf",
"Doc_type": "<Doc Type>",
"Name":"<Name>"
}
files=[]
headers= {}
# make authenticated request and print the response, status_code
resp = authed_session.post(url, headers=headers, json=payload, files=files)
print(resp.status_code)
print(resp.text)
