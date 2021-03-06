# Azure Key Vault Python Client Library
The purpose of this doc is to detail the API surface area of the Azure Key Vault client library for python, as well as to highlight 
the changes between the existing client library and the proposed changes for track 2.

## Namespaces and Packaging
Because this document proposes a substancial breaking change to the currently released Key Vault client library, both the library package, 
along with the root namespace must change.  To conform to naming guidelines , the package and root namespace of the Key Vault client library 
will change from __azure.keyvault__ to __azure.security.keyvault__.

#### Open Questions:
- Should the namespace and package names be roughly aquivalent accross languages?
- Should all client and model classes exist in the root namespace or should they be devided as proposed below for client refactoring?  
    - I.E. should the class Secret live directly in azure.security.keyvault, or in azure.security.keyvault.secrets?
- Should the service namespace keyvault be under azure (major version upgrade only) directly or should there be a group?  azure.keyvault vs. azure.security.keyvault


## Client class refactoring

In the current Key Vault client library all Key Vault data plane APIs are exposed through a single client.  This client api surface is 
autogenerated, and is implemented in the following class.

~~~ python
class KeyVaultClient(MultiApiClientMixin):

    def __init__(self, credentials):

    def create_key(self, vault_base_url, key_name, kty, key_size=None, key_ops=None, key_attributes=None, tags=None, curve=None, custom_headers=None, raw=False, **operation_config):
    def import_key(self, vault_base_url, key_name, key, hsm=None, key_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    def update_key(self, vault_base_url, key_name, key_version, key_ops=None, key_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    def delete_key(self, vault_base_url, key_name, custom_headers=None, raw=False, **operation_config):
    def get_key(self, vault_base_url, key_name, key_version, custom_headers=None, raw=False, **operation_config):
    def get_keys(self, vault_base_url, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def get_key_versions(self, vault_base_url, key_name, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def get_deleted_key(self, vault_base_url, key_name, custom_headers=None, raw=False, **operation_config):
    def get_deleted_keys(self, vault_base_url, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def purge_deleted_key(self, vault_base_url, key_name, custom_headers=None, raw=False, **operation_config):
    def recover_deleted_key(self, vault_base_url, key_name, custom_headers=None, raw=False, **operation_config):
    def backup_key(self, vault_base_url, key_name, custom_headers=None, raw=False, **operation_config):
    def restore_key(self, vault_base_url, key_bundle_backup, custom_headers=None, raw=False, **operation_config):
    def encrypt(self, vault_base_url, key_name, key_version, algorithm, value, custom_headers=None, raw=False, **operation_config):
    def decrypt(self, vault_base_url, key_name, key_version, algorithm, value, custom_headers=None, raw=False, **operation_config):
    def sign(self, vault_base_url, key_name, key_version, algorithm, value, custom_headers=None, raw=False, **operation_config):
    def verify(self, vault_base_url, key_name, key_version, algorithm, digest, signature, custom_headers=None, raw=False, **operation_config):
    def wrap_key(self, vault_base_url, key_name, key_version, algorithm, value, custom_headers=None, raw=False, **operation_config):
    def unwrap_key(self, vault_base_url, key_name, key_version, algorithm, value, custom_headers=None, raw=False, **operation_config):
    def set_secret(self, vault_base_url, secret_name, value, tags=None, content_type=None, secret_attributes=None, custom_headers=None, raw=False, **operation_config):
    def update_secret(self, vault_base_url, secret_name, secret_version, content_type=None, secret_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    def delete_secret(self, vault_base_url, secret_name, custom_headers=None, raw=False, **operation_config):
    def get_secret(self, vault_base_url, secret_name, secret_version, custom_headers=None, raw=False, **operation_config):
    def get_secrets(self, vault_base_url, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def get_secret_versions(self, vault_base_url, secret_name, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def get_deleted_secret(self, vault_base_url, secret_name, custom_headers=None, raw=False, **operation_config):
    def get_deleted_secrets(self, vault_base_url, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def purge_deleted_secret(self, vault_base_url, secret_name, custom_headers=None, raw=False, **operation_config):
    def recover_deleted_secret(self, vault_base_url, secret_name, custom_headers=None, raw=False, **operation_config):
    def backup_secret(self, vault_base_url, secret_name, custom_headers=None, raw=False, **operation_config):
    def restore_secret(self, vault_base_url, secret_bundle_backup, custom_headers=None, raw=False, **operation_config):
    def get_certificates(self, vault_base_url, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def delete_certificate(self, vault_base_url, certificate_name, custom_headers=None, raw=False, **operation_config):
    def set_certificate_contacts(self, vault_base_url, contact_list=None, custom_headers=None, raw=False, **operation_config):
    def get_certificate_contacts(self, vault_base_url, custom_headers=None, raw=False, **operation_config):
    def delete_certificate_contacts(self, vault_base_url, custom_headers=None, raw=False, **operation_config):
    def get_certificate_issuers(self, vault_base_url, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def set_certificate_issuer(self, vault_base_url, issuer_name, provider, credentials=None, organization_details=None, attributes=None, custom_headers=None, raw=False, **operation_config):
    def update_certificate_issuer(self, vault_base_url, issuer_name, provider=None, credentials=None, organization_details=None, attributes=None, custom_headers=None, raw=False, **operation_config):
    def get_certificate_issuer(self, vault_base_url, issuer_name, custom_headers=None, raw=False, **operation_config):
    def delete_certificate_issuer(self, vault_base_url, issuer_name, custom_headers=None, raw=False, **operation_config):
    def create_certificate(self, vault_base_url, certificate_name, certificate_policy=None, certificate_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    def import_certificate(self, vault_base_url, certificate_name, base64_encoded_certificate, password=None, certificate_policy=None, certificate_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    def get_certificate_versions(self, vault_base_url, certificate_name, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def get_certificate_policy(self, vault_base_url, certificate_name, custom_headers=None, raw=False, **operation_config):
    def update_certificate_policy(self, vault_base_url, certificate_name, certificate_policy, custom_headers=None, raw=False, **operation_config):
    def update_certificate(self, vault_base_url, certificate_name, certificate_version, certificate_policy=None, certificate_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    def get_certificate(self, vault_base_url, certificate_name, certificate_version, custom_headers=None, raw=False, **operation_config):
    def update_certificate_operation(self, vault_base_url, certificate_name, cancellation_requested, custom_headers=None, raw=False, **operation_config):
    def get_certificate_operation(self, vault_base_url, certificate_name, custom_headers=None, raw=False, **operation_config):
    def delete_certificate_operation(self, vault_base_url, certificate_name, custom_headers=None, raw=False, **operation_config):
    def merge_certificate(self, vault_base_url, certificate_name, x509_certificates, certificate_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    def get_deleted_certificates(self, vault_base_url, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def get_deleted_certificate(self, vault_base_url, certificate_name, custom_headers=None, raw=False, **operation_config):
    def purge_deleted_certificate(self, vault_base_url, certificate_name, custom_headers=None, raw=False, **operation_config):
    def recover_deleted_certificate(self, vault_base_url, certificate_name, custom_headers=None, raw=False, **operation_config):
    def get_storage_accounts(self, vault_base_url, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def delete_storage_account(self, vault_base_url, storage_account_name, custom_headers=None, raw=False, **operation_config):
    def get_storage_account(self, vault_base_url, storage_account_name, custom_headers=None, raw=False, **operation_config):
    def set_storage_account(self, vault_base_url, storage_account_name, resource_id, active_key_name, auto_regenerate_key, regeneration_period=None, storage_account_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    def update_storage_account(self, vault_base_url, storage_account_name, active_key_name=None, auto_regenerate_key=None, regeneration_period=None, storage_account_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    def regenerate_storage_account_key(self, vault_base_url, storage_account_name, key_name, custom_headers=None, raw=False, **operation_config):
    def get_sas_definitions(self, vault_base_url, storage_account_name, maxresults=None, custom_headers=None, raw=False, **operation_config):
    def delete_sas_definition(self, vault_base_url, storage_account_name, sas_definition_name, custom_headers=None, raw=False, **operation_config):
    def get_sas_definition(self, vault_base_url, storage_account_name, sas_definition_name, custom_headers=None, raw=False, **operation_config):
    def set_sas_definition(self, vault_base_url, storage_account_name, sas_definition_name, parameters, sas_definition_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    def update_sas_definition(self, vault_base_url, storage_account_name, sas_definition_name, parameters=None, sas_definition_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
~~~

This large API surface on the KeyVaultClient makes for a less than optimal user experience as it can be difficult to find the appropriate API.  Also
is just the sheer overload of information when reading through docs code or scrolling through intellisense. 

## Logical Grouping of Client APIs
In the track 2 KeyVaultClient we propose breaking up the APIs of KeyVaultClient into logical groups based off the entity they deal with, 
and creating a more refined client for each group with a high level convenience client which exposes all of them.  The APIs will be grouped 
in to three client classes __SecretClient__, __KeyClient__, and __CertificateClient__.  A convenience client, __KeyVaultClient__, will 
expose the other three clients as read only properties for users whose scenarios use APIs on more than one client.

## __KeyVaultClient__
~~~ python
class KeyVaultClient(MultiApiClientMixin):
    
    def __init__(self, vaultUrl, credentials, pipeline=None):

    @property
    def secrets(self):
        """
        :rtype:`azure.security.keyvault.SecretClient`
        """
    
    @property
    def keys(self):
        """
        :rtype:`azure.security.keyvault.KeyClient`
        """
    
    @property
    def certificates(self):
        """
        :rtype:`azure.security.keyvault.CertificateClient`
        """
~~~

### Usage:
~~~ python
from azure.security.keyvault import KeyVaultClient
from azure.authentication import DefaultCredentialProvider as default_credentials

client = KeyVaultClient('https://myvault.vaults.azure.net/', default_credentials)

secret = client.secrets.set('user1pass', 'password')

key = client.keys.get('key1')
~~~

#### Open Questions:
- How do I use a custom pipeline in python?
- Can the pipeline be passed as an __init__ arg?

## __SecretClient__
~~~ python
class SecretClient(MultiApiClientMixin):
    
    def __init__(self, vaultUrl, credentials, pipeline=None):
    
    def get(self, name, version=None, **kwargs):
    def set(self, name, value, tags=None, content_type=None, attributes=None, **kwargs):
    def update(self, name, version, content_type=None, attributes=None, tags=None, **kwargs):
    def list(self, max_page_size=None, **kwargs):
    def list_versions(self, name, max_page_size=None, **kwargs):
    def backup(self, name, **kwargs):
    def restore(self, backup, **kwargs):
    def delete(self, name, **kwargs):
    def get_deleted(self, name, **kwargs):
    def list_deleted(self, max_page_size=None, **kwargs):
    def purge_deleted(self, name, **kwargs):
    def recover_deleted(self, name, **kwargs):
~~~

#### Open Questions:
- Collapse the advanced usage parameters __custom_headers__, __raw__, and __**operation_config__ down to kwargs.  Thoughts?


## Initializing the client

~~~ python
def __init__(self, vaultUrl, credentials, pipeline=None):
    """
    :type vaultUrl: str
    :type credentials: azure.authenctication.Credential or azure.authenctication.CredentialProvider
    :type pipeline: ???
    """
~~~
### Usage
~~~ python
from azure.security.keyvault import SecretClient
from azure.authentication import DefaultCredentialProvider as default_credentials

secret = SecretClient('https://myvault.vaults.azure.net/', default_credentials)

secret = secrets.set('user1pass', 'password')

secret = secrets.get('user1pass')
~~~


## Get and Set Methods

## __SecretClient.set__
~~~ python
def set(self, name, value, tags=None, content_type=None, attributes=None, **kwargs):
    """
    :type name: str
    :type value: str
    :type content_type: str
    :type tags: dict[str, str]
    :type attributes: azure.security.keyvault.VaultAttributes
    :rtype: azure.security.keyvault.Secret or msrest.pipeline.ClientRawResponse
    """
~~~
#### Open Questions:
- This uses the existing pattern of returning ClientRawResponse when raw=True is specified.  Should this change?

#### Usage:
~~~ python
from azure.security.keyvault import SecretClient
from azure.authentication import DefaultCredentialProvider as default_credentials

secrets = SecretClient('https://myvault.vaults.azure.net/', default_credentials)

# set a simple secret such as password
pwd_secret = secrets.set('user1pass', 'password')

# set a symmetric key secret with nbf and exp
attr = VaultAttributes( not_before = datetime.utcnow(), expires = datetime.utcnow() + timedelta(days=90) }

key_secret = secrets.set('secretkey', base64.urlsafe_b64encode(keyMaterial), contentType='application/octect-stream', attributes=attr);
~~~
### Replaces
~~~ python
def set_secret(self, vault_base_url, secret_name, value, tags=None, content_type=None, secret_attributes=None, custom_headers=None, raw=False, **operation_config):
    """
    :type vault_base_url: str
    :type secret_name: str
    :type value: str
    :type tags: dict[str, str]
    :type content_type: str
    :type secret_attributes: azure.keyvault.v2016_10_01.models.SecretAttributes
    :param dict custom_headers: headers that will be added to the request
    :param bool raw: returns the direct response alongside the
        deserialized response
    :param operation_config: :ref:`Operation configuration
        overrides<msrest:optionsforoperations>`.
    :return: SecretBundle or ClientRawResponse if raw=true
    :rtype: ~azure.keyvault.v2016_10_01.models.SecretBundle or
        ~msrest.pipeline.ClientRawResponse
    """
~~~
#### Usage:
~~~ python
from azure.keyvault import KeyVaultClient
from azure.common.credentials import ServicePrincipalCredentials

credentials = ServicePrincipalCredentials(client_id='XXXX', secret='YYYY', tenant='ZZZZ')

client = KeyVaultClient(credentials)

# set a simple secret such as password
pwd_secret = client.set_secret('https://myvault.vaults.azure.net/', 'user1pass', 'password')

# set a symmetric key secret with nbf and exp
attr = SecretAttributes(not_before=datetime.utcnow(), expires=datetime.utcnow() + timedelta(days=90))

key_secret = client.set_secret('https://myvault.vaults.azure.net/', 
                               'secretkey',
                                base64.urlsafe_b64encode(keyMaterial), 
                                contentType='application/octect-stream', 
                                attributes=attr)
~~~

## __SecretClient.get__
~~~ python
def get(self, name, version=None, **kwargs):
    """
    :type name: str
    :type version: str
    :rtype: azure.security.keyvault.Secret or msrest.pipeline.ClientRawResponse
    """
~~~
#### Usage:
~~~ python
# get the latest version of a secret
secret = secrets.get('user1pass')

# get a specific version of a secret
secret = secrets.get('user1pass', '6A385B124DEF4096AF1361A85B16C204')
~~~
### Replaces
~~~ python
def get_secret(self, vault_base_url, secret_name, secret_version, custom_headers=None, raw=False, **operation_config):
    """
    :type vault_base_url: str
    :type secret_name: str
    :type secret_version: str
    :param dict custom_headers: headers that will be added to the request
    :param bool raw: returns the direct response alongside the
        deserialized response
    :param operation_config: :ref:`Operation configuration
        overrides<msrest:optionsforoperations>`.
    :return: SecretBundle or ClientRawResponse if raw=true
    :rtype: ~azure.keyvault.v2016_10_01.models.SecretBundle or
        ~msrest.pipeline.ClientRawResponse
    """
~~~
#### Usage:
~~~ python
# get the latest version of a secret
secret_bundle = client.get_secret('https://myvault.vaults.azure.net/', 'user1pass')

# get a specific version of a secret
secret_bundle = client.get_secret('https://myvault.vaults.azure.net/', 'user1pass', '6A385B124DEF4096AF1361A85B16C204')
~~~
## __SecretClient.update__
~~~ python
def update(self, secret, **kwargs):
    """
    :type secret: azure.security.keyvault.Secret
    :rtype: azure.security.keyvault.Secret or msrest.pipeline.ClientRawResponse
    """
~~~
#### Usage:
~~~ python
# get the latest version of a secret
secret = secrets.get('user1pass')

# extend the expiration date
secret.attributes.expires = secret.attributes.expires + timedelta(days=90)

updated = secrets.update(secret)
~~~
#### Open questions:
- Secret value is not returned from the service Update call.  Should this be carried over from the specified secret (as could not have been updated)?

### Replaces
~~~ python
def update_secret(self, vault_base_url, secret_name, secret_version, content_type=None, secret_attributes=None, tags=None, custom_headers=None, raw=False, **operation_config):
    """
    :type vault_base_url: str
    :type secret_name: str
    :type secret_version: str
    :type content_type: str
    :type secret_attributes: azure.keyvault.v2016_10_01.models.SecretAttributes
    :type tags: dict[str, str]
    :param dict custom_headers: headers that will be added to the request
    :param bool raw: returns the direct response alongside the deserialized response
    :param operation_config: :ref:`Operation configuration overrides<msrest:optionsforoperations>`.
    :rtype: ~azure.keyvault.v2016_10_01.models.SecretBundle or
        ~msrest.pipeline.ClientRawResponse
    """
~~~
#### Usage:
~~~ python
# get the latest version of a secret
secret_bundle = client.get_secret('https://myvault.vaults.azure.net/', 'user1pass')

# extend the expiration date
secret.attributes.expires = secret.attributes.expires + timedelta(days=90)

# we need to parse the secret id to get the version of the latest secret
secret_id = SecretId(secret_bundle.id)

secret_bundle = client.get_secret(secret_id.vault_url, secret_id.name, secret_id.version, attributes=secret.attributes)
~~~

## __list and list_versions__
~~~ python
def list(self, max_page_size=None, **kwargs):
    """
    :type max_page_size: int 
    :rtype: azure.security.keyvault.SecretPaged[azure.security.keyvault.Secret]
    """
def list_versions(self, name, maxresults=None, **kwargs):
    """
    :type name: str
    :type max_page_size: int 
    :rtype: azure.security.keyvault.SecretPaged[azure.security.keyvault.Secret]
    """
~~~
#### Usage:
~~~ python
# find all expired secrets
expired_secrets = [s for s in secrets.list() if s.attributes.expires >= datetime.utcnow()]

# get the first version of a secret
first_version = min(secrets.list(), key=attrgetter('attributes.created'))
~~~
#### Open Questions:
- Is list to generic of an API name for python, to be confused with built it list()?
### Replaces:
~~~ python
def get_secrets(self, vault_base_url, maxresults=None, custom_headers=None, raw=False, **operation_config):
    """
    :type vault_base_url: str
    :type maxresults: int
    :param dict custom_headers: headers that will be added to the request
    :param bool raw: returns the direct response alongside the deserialized response
    :param operation_config: :ref:`Operation configuration overrides<msrest:optionsforoperations>`.
    :return: An iterator like instance of SecretItem
    :rtype: azure.keyvault.v2016_10_01.models.SecretItemPaged[azure.keyvault.v2016_10_01.models.SecretItem]
    """
def get_secret_versions(self, vault_base_url, secret_name, maxresults=None, custom_headers=None, raw=False, **operation_config):
    """
    :type vault_base_url: str
    :type secret_name: str
    :type maxresults: int
    :param dict custom_headers: headers that will be added to the request
    :param bool raw: returns the direct response alongside the deserialized response
    :param operation_config: :ref:`Operation configuration overrides<msrest:optionsforoperations>`.
    :rtype: ~azure.keyvault.v2016_10_01.models.SecretItemPaged[~azure.keyvault.v2016_10_01.models.SecretItem]
    """
~~~
#### Usage:
~~~ python
# find all expired secrets
expired_secrets = [s for s in client.get_secrets('https://myvault.vaults.azure.net/') if s.attributes.expires >= datetime.utcnow()]

# get the first version of a secret
first_version = min(client.get_secrets('https://myvault.vaults.azure.net/'), key=attrgetter('attributes.created'))
~~~

## __delete, recover, and purge__
~~~ python
def delete(self, name, **kwargs):
    """
    :type secret_name: str
    :rtype: ~azure.security.keyvault.DeletedSecret or ~msrest.pipeline.ClientRawResponse
    """
def recover_deleted(self, name, **kwargs):
    """
    :type secret_name: str
    :rtype: ~azure.security.keyvault.Secret or ~msrest.pipeline.ClientRawResponse
    """
def purge_deleted(self, name, **kwargs):
    """
    :type secret_name: str
    :rtype: None or ~msrest.pipeline.ClientRawResponse
    """
~~~
#### Usage:
~~~ python
# delete a secret
secrets.delete('SecretToRecover')
secrets.delete('SecretToPurge')

# recover a secret
secrets.recover('SecretToRecover')

# purge a secret
secrets.purge('SecretToPurge')
~~~
### Replaces:
~~~ python
def delete_secret(self, vault_base_url, secret_name, custom_headers=None, raw=False, **operation_config):
    """
    :type vault_base_url: str
    :type secret_name: str
    :param dict custom_headers: headers that will be added to the request
    :param bool raw: returns the direct response alongside the deserialized response
    :param operation_config: :ref:`Operation configuration overrides<msrest:optionsforoperations>`. 
    :rtype: ~azure.keyvault.v2016_10_01.models.DeletedSecretBundle or ~msrest.pipeline.ClientRawResponse
    """
def recover_deleted_secret(self, vault_base_url, secret_name, custom_headers=None, raw=False, **operation_config):
    """
    :type vault_base_url: str
    :type secret_name: str
    :param dict custom_headers: headers that will be added to the request
    :param bool raw: returns the direct response alongside the deserialized response
    :param operation_config: :ref:`Operation configuration overrides<msrest:optionsforoperations>`.
    :rtype: ~azure.keyvault.v2016_10_01.models.SecretBundle or ~msrest.pipeline.ClientRawResponse
    """
def purge_deleted_secret(self, vault_base_url, secret_name, custom_headers=None, raw=False, **operation_config):
    """
    :type vault_base_url: str
    :type secret_name: str
    :param dict custom_headers: headers that will be added to the request
    :param bool raw: returns the direct response alongside the deserialized response
    :param operation_config: :ref:`Operation configuration overrides<msrest:optionsforoperations>`.
    :rtype: None or ~msrest.pipeline.ClientRawResponse
    """
~~~
#### Usage:
~~~ python
# delete a secret
client.delete_secret('https://myvault2.vaults.azure.net/', 'SecretToRecover')
secrets.delete('https://myvault2.vaults.azure.net/', 'SecretToPurge')

# recover a secret
secrets.recover('https://myvault2.vaults.azure.net/', 'SecretToRecover')

# purge a secret
secrets.purge('https://myvault2.vaults.azure.net/', 'SecretToPurge')
~~~

## __backup and restore__
~~~ python
def backup(self, name, **kwargs):
    """
    :type name: str 
    :rtype: bytes
    """
def restore(self, backup, **kwargs):
    """
    :type backup: bytes 
    :rtype: azure.security.keyvault.Secret or msrest.pipeline.ClientRawResponse
    """
~~~
#### Usage:
~~~ python
# backup a secret to a file
secret_backup = secrets.backup('user1pass')
with open('secret.bak', 'wb') as f:
    f.write(secret_backup)

# delete the secret
secrets.delete('user1pass')

# restore the secret from the file backup
with open('secret.bak', 'rb') as f:
    secret_backup = f.read()
secrets.restore(secret_backup)

# move the secret to a different vault
vault2_secrets = SecretClient('https://myvault2.vaults.azure.net/', default_credentials)
vault2_secrets.resore(secret_backup)
~~~
### Replaces:
~~~ python
def backup_secret(self, vault_base_url, secret_name, custom_headers=None, raw=False, **operation_config):
    """
    :type vault_base_url: str
    :type secret_name: str
    :param dict custom_headers: headers that will be added to the request
    :param bool raw: returns the direct response alongside the deserialized response
    :param operation_config: :ref:`Operation configuration overrides<msrest:optionsforoperations>`.
    :rtype: azure.keyvault.v2016_10_01.models.BackupSecretResult or msrest.pipeline.ClientRawResponse
    """
def restore_secret(self, vault_base_url, secret_bundle_backup, custom_headers=None, raw=False, **operation_config):
    """
    :type vault_base_url: str
    :type secret_bundle_backup: bytes
    :param dict custom_headers: headers that will be added to the request
    :param bool raw: returns the direct response alongside the deserialized response
    :param operation_config: :ref:`Operation configuration overrides<msrest:optionsforoperations>`.
    :rtype: ~azure.keyvault.v2016_10_01.models.SecretBundle or ~msrest.pipeline.ClientRawResponse
    """
~~~
#### Usage:
~~~ python
# backup a secret to a file
secret_backup = client.backup_secret('https://myvault.vaults.azure.net/', 'user1pass')
with open('secret.bak', 'wb') as f:
    f.write(secret_backup)

# delete the secret
client.delete_secret('https://myvault.vaults.azure.net/', 'user1pass')

# restore the secret from the file backup
with open('secret.bak', 'rb') as f:
    secret_backup = f.read()
client.restore_secret('https://myvault.vaults.azure.net/', secret_backup)

# move the secret to a different vault
client.resore_secret('https://myvault2.vaults.azure.net/', secret_backup)
~~~
