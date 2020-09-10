[title]: # (Change Password)
[tags]: # (blue prism,dsv)
[priority]: # (3)
# Use case: change password for service account

For implementation this task usage visual business object [Login Agent](https://usermanual.wiki/Pdf/Blue20Prism20User20Guide2020Login20Agent.779174028/view)

When executing an automated process on a Blue Prism Runtime Resource, it is necessary for the Runtime Resource to be listening on a device which is logged in and not locked.  This allows the process to operate under the context of that user and provides access to all of the local applications and network resources that it may need.

Login Agent provides a mechanism that is intended to assist with automating the logging of a device into Windows such that the main Blue Prism RuntimeResource can be started. This includes:

- Configuring the Login Agent service with appropriate information to launch a Login Agent Runtime Resource.

- A Login Agent Runtime Resource being started automatically when a device is powered on (or rebooted) that connects to the appropiate Blue Prism environment.

- The Login Agent Runtime Resource being instructed (manually or via a schedule) to Log in.

- The Login Agent securely retrieving the appropriate credential from the database and using this to authenticate with Windows.

  **Input params**

> ``<path>`` path secret for DSV value ``string``
>
> ``<service account>`` account Windows machine for change credentials
>
> ``CredentialName`` credential name where store access params
>
>> property ``client_id`` access params store in the credential vault Blue Prism
>>
>> property ``client_secret`` access params store in the credential vault Blue Prism
>>
>> property ``server_url`` access params store in the credential vault Blue Prism

This method changes the password stored in the credential vault Blue Prism to a valid password taken from the Thycotic DevOps Secret Vault.

Then using methods from the VBO Login Agent changes it on the resource machine.

Let's consider in more detail:

- We read the list of all credentials from the credential store Blue Prism, check for coincidence with the input name of the secret.

- If successful, then exit the read loop, throwing exception.

- Read all params from credential, username, password, expirate date, status.

- Get data from DSV by input path parameter

- Compare the passwords received from the credential store Blue Prism  and the DSV, if the passwords match, we finish.

- if the passwords did not match, then using the method ChangePassword (VBO LoginAgent) we change the password to the one received from the DSV

- Store password from DSV in the credential vault Blue Prism.

## Scheme

![UpdatePasswordServiceAccount](Images/UpdateServiceAccount.png)
