# KMS Overview
- Managed: managed service that makes it easy for you to create and control the encryption keys used to encrypt your data
- Integrated: seamlessly integrated with many aws services 
- Simple: simple to encrypt your data with encryption keys that you manage
=> Use when you are dealing with sensitive information that you want to keep secret (customer data, financial data, database password, secrets, credentials, etc.)
=> KMS is multi-tenant whereas CloudHSM is dedicated hardware

# CMK Overview
- Customer Master key: encrypt/decrypt data up to 4KB
- Used for generate / encrypt / decrypt data key
- Data key: used to encrypt / decrypt your data which is over four 4KB
- CMK has:
	- Alias: application can refer to the alias when using the CMK
	- Creation date: date and time CMK was created
	- Description
	- Key state: enabled, disabled, pending, deletion, unavailable
	- Key material: customer provided or aws provided
	- Stay inside KMS: can never be exported
- Set up CMK: Create alias + description -> choose key material
- Key administrative permissions: IAM users and roles that can administer (but not use) the key through the KMS API
- Key usage permission: IAM users and roles that can use the key to encrypt and decrypt data
## Creating a CMK 
- Create group myKMSGroup in IAM / Group
- Create user myKeyManager in IAM / User
- Add user to group
- Select service Key Management Service (KMS)
- Select key type:
	- Symmetric: một encryption key dùng cho cả encrypt và decrypt
	- Asymmetric: một cặp public/private key pair dùng để encrypt/decrypt hoặc kí/verify operation => nếu cần encrypt / decrypt data ở ngoài aws thì nên dùng asymmetric key vì có public key
- Tạo key ở region nào thì chỉ dùng được ở region đấy
-  Chọn key material origin:  cryptography secrets which are going to be used for the encryption and decryption processes
	-  KMS: KMS tự generate key material
	-  External: key material từ external source
	-  Custom key store: sử dụng custom key lưu ở CloudHSM (CloudHSM for customers operate in a highly regulated environment)
- Create alias + description + tag
- Add key administrators: who do we want to be able to administer the key using KMS API => select user myKeyManager
- Define key usage permission: who do we want to be able to use the key to encrypt and decrypt data => select user myKeyUser
- Review and edit key policy
=> Tạo CMK thành công
- With CMK when you delete a key it won't be deleted immediately but you actually get a scheduled deletion date => Can cancel the key deletion within one week because once you deleted it the files you encrypted with are not going to be able to be accessed
- AWS will create a CMK for each of the services that you are using

# KMS Api calls
- encrypt file:
  ```
  aws kms encrypt --key-id KEYIDHERE --plaintext fileb://secret.txt --output text --query CiphertextBlob | base64 --decode > encryptedsecret.txt
  ```
 - decrypt file
  ```
  aws kms decrypt --ciphertext-blob fileb//encryptedsecret.txt --output text --query Plaintext | base64 --deocde > decryptedsecret.txt
  ```
 - re-encrypt file => use when you wanted to manually rototate, refresh CMK or your CMK has been compromised
  ```
  aws kms re-encrypt --destination-key-id KEYIDHERE --ciphertext-blob fileb/encryptedsecret.txt | base64 > newencryption.txt
  ```
- rotate key => aws will rotate your CMK on an annual basis
  ``` 
  aws kms enable-key-rotation --key-id KEYIDHERE
  ```
- Check key rotation status
	```
	aws kms get-key-rotation-status --key-id KEYIDHERE
	```
- Generate data key for encrypting/decrypting file is over 4kb
	```
	aws kms generate-data-key --key-id KEYIDHERE --key-spec AES_256
	```
	
# Envelope Encryption
- A process for encrypting your data. It applies to file > 4 KB
- The envelope encryption process
	- A CMK in KMS 
	- Generate a data key (envelope key) with the CMK
	- This data key will be used to encrypt data
	- The encrypted data key is stored locally with the data so it can be used later on for decryption purposes and KMS does not store the data key anywhere.
- The envelope decryption process
	- KMS API use the cmk and run a decrypt operation and return the data key in plain text form
	- The plain text data key is used to decrypt the data
	- The plain text data then is deleted from the memory
- Why use envelope encryption?
=> Network: when you encrypt data directly with KMS it must be transferred over the network
=> Performance: only the data key goes over the network, not your data
=> Benefits: the data key is used locally in your app or aws service, avoid the need to transfer large amounts of data to KMS
=> Envelope encryption is used to protect your encryption key