# Section B – Digital Certificates

## Aim

The aim of this section was to work with digital certificates stored in PFX files. I used OpenSSL to inspect the certificates and Python to test passwords and display certificate information.

## Environment

The practical work was completed on my existing Google Cloud Ubuntu VM.

- Operating system: Ubuntu 22.04.5 LTS
- OpenSSL: 3.0.2
- Python: 3.10.12
- pyOpenSSL: 21.0.0

## Preparing the certificate files

I downloaded the certificate archive supplied for the lab. The `unzip` command was not installed on the VM, so I used Python's `zipfile` module to check the archive and extract the files.

The archive integrity test completed successfully.

## Inspecting a PFX file with OpenSSL

I first tried to inspect `bill01.pfx` with OpenSSL. The command failed because the file uses RC2-40-CBC, which is a legacy algorithm and is not enabled by default in OpenSSL 3.

I repeated the command with the `-legacy` option. This allowed OpenSSL to read the file.

The PFX information showed a SHA-1 MAC, Triple DES encryption for the key bag and RC2-40-CBC encryption for the certificate data.

## Certificate information

I extracted the certificate from `bill01.pfx` and displayed its details.

- Subject: CN = Test
- Issuer: CN = Test
- Version: X.509 version 3
- Public key: RSA, 4096 bits
- Signature algorithm: SHA-512 with RSA
- Basic constraint: CA:TRUE
- Valid from: 27 September 2015
- Valid until: 31 December 2039

The subject and issuer are the same, which is consistent with a self-signed certificate.

## Checking the certificate signature

I saved the extracted certificate as a PEM file and used OpenSSL to verify its signature against itself.

The command returned:

```text
/tmp/bill01-cert.pem: OK
```

This confirms that the certificate's self-signature was verified using its own public key. It does not mean that the certificate is trusted by a browser, operating system or certificate authority.

## Python password-testing exercise

I used a Python script to test the candidate passwords provided for the lab against the supplied `fredpfx.pfx` file. The script called OpenSSL and checked whether it could successfully extract the certificate.

The test identified a working password. I have not included the password in this public repository because it is not necessary for documenting the method.

The extracted certificate had the subject CN = fred and serial number FCA689.

## Displaying certificate details with pyOpenSSL

I then modified the Python script to load the extracted certificate with pyOpenSSL and display the information required by the worksheet.

The script displayed:

- Certificate version: 3
- Subject: C = US, ST = Lothian, L = Edinburgh, O = Test, emailAddress = fred@home, CN = fred
- Valid from: 8 January 2010 at 17:37:58 UTC
- Valid until: 8 January 2012 at 17:37:58 UTC
- Public key algorithm: RSA
- Public key size: 1024 bits
- Public key: displayed in PEM format

The certificate's validity period had ended, so it was expired when I completed this exercise.

## What I learned

- PFX files can contain certificates and private keys protected by a password.
- OpenSSL 3 may need the `-legacy` option to read files that use older encryption algorithms.
- A certificate can be self-signed, but that does not automatically make it trusted.
- A certificate's subject, issuer, serial number, validity period and public key can be inspected with OpenSSL.
- Python can automate password testing and certificate inspection.
- pyOpenSSL can be used to read certificate fields and public key information.

## Security note

The PFX files, private keys and recovered password have not been added to this repository. Only the lab notes and safe evidence should be published.

## Conclusion

In this section I used OpenSSL and Python to inspect PFX files, identify certificate information, verify a self-signed certificate and display certificate details with pyOpenSSL. I also learned why some older PFX files need the legacy option when they are opened with OpenSSL 3.
