# cose-meta-map

This document specifies a COSE header, containing a collection of key => value pairs, providing interchangeable metadata in a COSE structure.

- **Tag value**: `<TBD>`
- **Data item**: Map (CBOR major type `5`)
- **Semantics**: Contains a collection of `key => value` pairs, constrained to type tstr.
The keys must be unique.
When processing a meta-map, if a key appears multiple times, the message MUST be rejected as malformed.

## Rationale

[RFC9052](https://datatracker.ietf.org/doc/rfc9052/) provides integrity protection for attached, detached and hashed payloads that may be shared between parties.
As a COSE structure is shared, stored and indexed, additional metadata may be required providing context to the attached, detached or hashed protected content.

[Section 3 of RFC9052](https://www.rfc-editor.org/rfc/rfc9052.html#name-header-parameters) supports additional labels (`* labels => values`) in the protected and unprotected [headers](https://www.rfc-editor.org/rfc/rfc9052#header-parameters).
The `meta-map` provides a structure of metadata within the headers, enabling interoperability between parties sharing a COSE structure.

## Examples

A collection of vCon properties, enabling interchange of metadata about a protected [cose-hash-envelope](https://datatracker.ietf.org/doc/draft-ietf-cose-hash-envelope/)
~~~json
{
  "meta-map": {
    "conserver_link": "scitt",
    "conserver_link_version": "0.2.0",
    "timestamp_declared": "2024-05-07T16:33:29.004994",
    "vcon_operation": "vcon_create",
    "vcon_draft_version": "01"
    }
}
~~~

**CDDL**

~~~cddl
COSE_Sign1 = [
  protected   : bstr .cbor Protected_Header,
  unprotected : bstr .cbor Unprotected_Header,
  payload     : bstr / nil,
  signature   : bstr
]

Protected_Header = {
  ? &(alg: 1) => int
  ? &(payload_hash_alg: -6800) => int
  ? &(payload_preimage_content_type: -6802) => tstr / uint
  ? &(payload_location: -6801) => tstr
  ? &(meta-map: TBD_1) => meta-map
}

Unprotected_Header = {
  * int => any
}
~~~

### Alternatives Considered

It's possible to embed the metadata within the payload:

```cddl
payload = {
    "meta-map":  { tstr => tstr},
    payload: bstr
}
```

However this forces consumers to understand the modified COSE structure.
Providing the optional meta-map header, middleware can choose to consume or ignore the header as the a producing and relying party may need to understand metadata about the payload.

## References

- [RFC9052](https://datatracker.ietf.org/doc/rfc9052/): COSE

- Label.259  
Consideration was give to registering [CBOR LABEL.259](https://github.com/shanewholloway/js-cbor-codec/blob/master/docs/CBOR-259-spec--explicit-maps.md) as a COSE header.
However the COSE meta-map is intentionally constrained to (`key:tstr => value:tstr`) pairs, providing metadata support within the COSE Headers, while not attempting to duplicate a COSE Payload.

## Implementations

[DataTrails](https://www.datatrails.ai/), [Strolid](https://strolid.com/), [vConGPT](https://vcongpt.com/)

Used as to interchange metadata between a vCon and a SCITT Ledger, providing integrity and inclusion protection of vCons.

vCons are typically large as they contain recordings and attachments, and may contain PII information which must be capable of being forgotten.
The proof a vCon was shared, consumed, consent provided and subsequent revocation of consent is recorded on a SCITT ledger.
The metadata enables the storing of enough information in DataTrails, recorded on a SCITT Ledger to verify the integrity, inclusion and intent of the vCon, while not storing any PII information that must be forgotten.

## Author

Steve Lasker ([DataTrails](https://www.datatrails.ai/))  
stevenlasker@hotmail.com
