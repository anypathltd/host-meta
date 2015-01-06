host-meta
=========
Host-Meta tells you about things operated by a domain.

RFC6415 defines a standardized way to publish metadata, including information about resources controlled by the host, for any domain. In contrast to the original spec, Ripple uses the JSON version of host-meta exclusively, and never queries for the XML version. This spec defines several Ripple-centric resources which a host can advertise in a host-meta file, so that other applications can automatically find the host’s other Gateway services, and verify the domain’s various Ripple assets. This extends and replaces the functionality of the ripple.txt spec.

The response to a host-meta request is a document called a JRD (JSON resource descriptor; there is also an XML version called an XRD).

Request Format

GET /.well-known/host-meta.json
Alternate request format:

GET /.well-known/host-meta
Accept: application/json
Note: Some domains that do not use Gateway Services may implement host-meta without providing a JRD. In this case, the alternate request format may return an XRD document instead of a Not Found error.

Here is an example of an entire host-meta JRD:

GET https://latambridgepay.com/.well-known/host-meta.json

{
    "subject": "https://latambridgepay.com",
    "expires": "2014-01-30T09:30:00Z",
    "properties": {
        "name": "Latam Bridge Pay",
        "description": "Ripple Gateway to and from Latin American banks.",
        "rl:type": "gateway",
        "rl:domain": "latambridgepay.com",
        "rl:accounts": [
            {
                "address": "r4tFZoa7Dk5nbEEaCeKQcY3rS5jGzkbn8a",
                "rl:currencies": [
                    "USD",
                    "BRL",
                    "PEN",
                    "MXN"
                ]
            }
        ],
        "rl:hotwallets": [
            "rEKuBLEX2nHUiGB9dCGPnFkA7xMyafHTjP"
        ]
    },
    "links": [
        {
            "rel": "lrdd",
            "template": "https://latambridgepay.com/.well-known/webfinger.json?q={uri}",
            "properties": {
                "rl:uri_schemes": ["paypal","bitcoin","acct","ripple"]
            }
        },
        {
            "rel": "https://gatewayd.org/gateway-services/bridge_payments",
            "href": "https://latambridgepay.com/v1/bridge_payments",
            "properties": {
                "version": "1",
                "additional_info_definitions":{
                    "bank": {
                      "type": "AstropayBankCode",
                      "label": {
                        "en": "Astropay Bank Code"
                      },
                      "description": {
                        "en": "Bank code from Astropay's documentation"
                      }
                    },
                    "country": {
                      "type": "AstropayCountryCode",
                      "label": {
                        "en": "Astropay Country Code",
                      },
                      "description": {
                        "en": "Country code from Astropay's documentation"
                      }
                    },
                    "cpf": {
                      "type": "PersonalIDNumber",
                      "label": {
                        "en": "Personal Government ID Number"
                      },
                      "description": {
                        "en": "Personal ID number from the Astropay documentation"
                      },
                    }
                    "name": {
                      "type": "FullName",
                      "label": {
                        "en": "Sender's Full Name"
                      },
                      "description": {
                        "en": "First and last name of sender"
                      }
                    },
                    "email": {
                      "type": "EmailAddress",
                      "label": {
                        "en": "Sender's Email Address"
                      },
                      "description": {
                        "en": "Sender's Email Address"
                      }
                    },
                    "bdate": {
                      "type": "Date",
                      "label": {
                        "en": "YYYYMMDD"
                      },
                      "description": {
                        "en": "Sender's date of birth"
                      }
                    }
                }
            }
        }
    ]
}
This file is divided up into the following elements:

Host-Meta Aliases
Aliases are NOT RECOMMENDED in host-meta, according to the spec. Consumers of Gateway Services should not rely on any content there.

Host-Meta Properties
    ...
    "properties": {
        "name": "Latam Bridge Pay",
        "description": "Ripple Gateway to and from Latin American banks.",
        "rl:type": "gateway",
        "rl:domain": "latambridgepay.com",
        "rl:accounts": [
            {
                "address": "r4tFZoa7Dk5nbEEaCeKQcY3rS5jGzkbn8a",
                "rl:currencies": [
                    "USD",
                    "BRL",
                    "PEN",
                    "MXN"
                ]
            }
        ],
        "rl:hotwallets": [
            "rEKuBLEX2nHUiGB9dCGPnFkA7xMyafHTjP"
        ]
    }
    ...
Ripple defines the following properties:

Property	Value	Description
rl:type	String	What type of Ripple entity this domain is. Valid types are "gateway", "merchant", "bridge", "wallet-service", "end-user". Other custom types are allowed.
rl:domain	String	The domain this host-meta document applies to, which should match the one it is being served from. For example, "coin-gate.com"
rl:accounts	Array of objects	Each member of this array should be an account definition object for an issuing account (cold wallet) operated by this host
rl:hotwallets	Array of strings	Each member of this array should be either a base-58-encoded Ripple Address or a Ripple Name for a hot wallet operated by the host
Gateway Services clients may also use the standard “name” and “description” properties.

TBD: format for providing a JWT-signing PubKey in the properties. (Possibly use OpenID format?)

Account Definition Object

An account definition object is a JSON object with the following properties:

Property	Value	Description
address	String	Base-58-encoded Ripple address
rl:name	String	(Optional) The Ripple Name of this account, omitting the leading tilde (~)
rl:currencies	Object	Map of currencies issued from this address, where field names are the currency codes and values are the maximum amount issued, as a string containing a decimal number.
Example account definition object:

{
    "address": "rLtys1YJHGj8oTpECWSzDv77YRGDWGduUX",
    "rl:currencies": {
        "BTC": "1000000"
    }
}
Host-Meta Links
The links field contains links to almost anything. The meaning of the link is identified by a relation type, typically a URI, in the rel field of the link object. Gateways should use links to advertise the other Gateway Services APIs that they offer.

Gateway Services requires the following links in host-meta:

Webfinger Link
Bridge Payments Link
(As additional services are defined, they should have links added to host-meta.)
