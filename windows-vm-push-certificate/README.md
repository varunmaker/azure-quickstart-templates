# Push a certificate onto a VM

<a href="https://azuredeploy.net" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Push a certificate onto a VM. Pass in the URL of the secret in KeyVault.

Pre-Requisistes - You need a certificate

These are the steps that need to be followed to upload the certificate into the KeyVault as a secret

1.	base64 encode the cert file
2.	Paste the base64 value into data field in this JSON object
          {
            “data”:”<Base64-encoded-file>”,
            “dataType” :”<file-format: pfx or cer>”,
            “password”:”<pfx-file-password>”
          }

3.	base64 the above JSON object
4.	Convert the base64 value into a secure string
$secret = ConvertTo-SecureString -String 'password' -AsPlainText –Force

5.	Then use the secure string value for the SecretValue in this cmdlet
          Set-AzureKeyVaultSecret -VaultName 'Contoso' -Name 'ITSecret' –SecretValue $secret

The following PowerShell script can make these steps easy

    $fileName = "C:\Users\kasing\Desktop\KayTest.pfx"
    $fileContentBytes = get-content $fileName -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "<fill-in>"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
    Set-AzureKeyVaultSecret -VaultName kayvault -Name testkay -SecretValue $secret
