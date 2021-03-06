# keycloak_openid_client_default_scopes

Allows for managing a Keycloak client's default client scopes. A default
scope that is attached to a client using the OpenID Connect protocol will
automatically use the protocol mappers defined within that scope to build
claims for this client regardless of the provided OAuth2.0 `scope` parameter.

Note that this resource attempts to be an **authoritative** source over
default scopes for a Keycloak client using the OpenID Connect protocol.
This means that once Terraform controls a particular client's default scopes,
it will attempt to remove any default scopes that were attached manually,
and it will attempt to add any default scopes that were detached manually.

By default, Keycloak sets the `profile` and `email` scopes as default scopes
for every newly created client. If you create this resource for the first
time and do not include these scopes, a following run of `terraform plan`
will result in changes.

### Example Usage

```hcl
resource "keycloak_realm" "realm" {
    realm   = "my-realm"
    enabled = true
}

resource "keycloak_openid_client" "client" {
    realm_id    = "${keycloak_realm.realm.id}"
    client_id   = "test-client"

    access_type = "CONFIDENTIAL"
}

resource "keycloak_openid_client_scope" "client_scope" {
    realm_id = "${keycloak_realm.realm.id}"
    name     = "test-client-scope"
}

resource "keycloak_openid_client_default_scopes" "client_default_scopes" {
    realm_id       = "${keycloak_realm.realm.id}"
    client_id      = "${keycloak_openid_client.client.id}"

    default_scopes = [
        "profile",
        "email",
        "${keycloak_openid_client_scope.client_scope.name}"
    ]
}

```

### Argument Reference

The following arguments are supported:

- `realm_id` - (Required) The realm this client and scopes exists in.
- `client_id` - (Required) The ID of the client to attach default scopes to. Note that this is the unique ID of the client generated by Keycloak.
- `default_scopes` - (Required) An array of client scope names to attach to this client.

### Import

This resource does not support import. Instead of importing, feel free to create this resource
as if it did not already exist on the server.
