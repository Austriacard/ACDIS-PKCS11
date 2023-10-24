# ACDIS Sapphire PKCS11
As part of the Sapphire product group our PKCS#11 driver enables the customer to manage cryptographic keys, and performing operations with them. It is a product that is used in conjunction with smartcard provided by Austriacard.

## Configuration for Fortify
The PKCS#11 module must be configured in the file card.json. This is located in the directory:
<<User-Home>> \.fortify

The following settings must be added to the “cards” section:

  "$schema": "./card.schema.json",
  "version": "1.1.13",
  "cards": [
    {
      "atr": "3BDF97008131FE4641434F5300020001010001010001015C",
      "name": "ACDIS Card",
      "driver": "11A050606FD48FAB340EA9F1FCB582A1E0F1B740"
    },
    …

In the “drivers” section, please add the following:

  "drivers": [
	…
    {
      "id": "11A050606FD48FAB340EA9F1FCB582A1E0F1B740",
      "name": "ACDIS",
      "file": {
        "windows": {
          "x86": "%WINDIR/SysWOW64/acdis-pkcs11.dll",
          "x64": "%WINDIR/System32/acdis-pkcs11-64.dll"
      	},
	"linux": "/home/linuxbrew/.linuxbrew/lib/libacdis-pkcs11.so",
	"osx": "/usr/local/lib/acdis-pkcs11.dylib"
      },
      "config": {
        "template": {
          "generate": {
            "private": {
              "token": true,
	      "sensitive": true
            },
            "public": {
              "token": true,
	      "sensitive": true
            },
	    "x509": {
              "token": true
            },
	    "request": {
              "token": true
            }
          },
          "import": {
            "private": {
              "token": true,
              "sensitive": true,
              "extractable": false
            },
            "public": {
              "token": true
            },
            "x509": {
              "token": true
            },
            "request": {
              "token": true
            }
          }
        }
      }
    },

Please note: 
Under Linux, the certificate (file “ca.pem”) located in the $HOME/.fortify directory must be installed into the trusted certificates of Mozilla Thunderbird.

## Known Issues in Fortify
Listed below are know issues in Fortify that might impact your use of Sapphire PKCS#11.

### Missing certificates in the list after refresh

Fortify's refresh function contains a bug: 

![Fortify_Refresh](https://github.com/Austriacard/ACDIS-PKCS11/assets/145022204/f5459075-d94b-426c-b464-dedc61641be0)

This error means that certificates are no longer displayed after a refresh. The list is empty. The certificates will only be displayed again after Fortify is restarted.

### Error while interpreting RSA/PSS certificates

Fortify cannot correctly interpret RSA/PSS certificates: error "hash: Missing required property".
