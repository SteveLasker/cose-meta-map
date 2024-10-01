---
title: "lasker-draft-cose-meta-map"
abbrev: "cmm"
category: info

docname: draft-lasker-cose-meta-map-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Security"
workgroup: "CBOR Object Signing and Encryption"
keyword:
 - cose
 - cbor
 - scitt
venue:
  group: "CBOR Object Signing and Encryption"
  type: "Working Group"
  mail: "cose@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/cose/"
  github: "SteveLasker/draft-lasker-meta-map"
  latest: "https://SteveLasker.github.io/draft-lasker-meta-map/draft-lasker-cose-meta-map.html"

author:
 -
    fullname: "Steve Lasker"
    organization: DataTrails
    email: "stevenlasker@hotmail.com"

normative:
  RFC9052: COSE

informative:
  LABEL.259:
    title: "CBOR Label 259"
    target: https://github.com/shanewholloway/js-cbor-codec/blob/master/docs/CBOR-259-spec--explicit-maps.md
    author:
      name: Shane Holloway
      org: ieee
    date: 2018

--- abstract

This document specifies a tag for a collection of key/value pairs in CBOR Representation, providing metadata in COSE headers.

--- middle

# Introduction

{{RFC9052}} provides integrity protection for attached, detached and hashed payloads that may be shared between parties.
As a COSE Envelope is shared, stored and indexed, additional metadata may be required to provide context to the attached, detached or hashed protected content.

{{Section 5.1 of RFC9052}} supports protected and unprotected [headers](https://www.rfc-editor.org/rfc/rfc9052#header-parameters) which supports additional labels, however the labels in each of the maps MUST be unique.
When processing messages, if a label appears multiple times, the message MUST be rejected as malformed, making a collection of string keys an invalid COSE structure.

COSE meta-map provides a CBOR map of key/value string pairs in CBOR Representation.
Consideration was give to registering CBOR {{LABEL.259}} as a COSE header.
However the COSE meta-map is intentionally constrained to key/value (string:string) pairs, providing metadata support within the COSE Headers, while not attempting to duplicate a COSE Payload.

## Examples

Given the following JavaScript instance:

~~~
new Map( [ ['k1', 'v1'], ['k2', 'v2'] ] )
~~~

The equivalent value as a set in CBOR diagnostic notation is:

~~~cbor
metamap = {
  TBD({"k1": "v1", "k2": "v2"})
}
~~~

The equivalent value as a set in CBOR diagnostic notation is:

~~~cbor-diag
D9 0103       # tag(meta-map)
  A2         # map(2)
      62      # text(2)
        6B31 # "k1"
      62      # text(2)
        7631 # "v1"
      62      # text(2)
        6B32 # "k2"
      62      # text(2)
        7632 # "v2"
~~~

A COSE Envelope, with a protected header referencing the above metamap:

~~~cddl
COSE_Sign1 = [
  protected   : bstr .cbor Protected_Header,
  unprotected : Unprotected_Header,
  payload     : bstr / nil,
  signature   : bstr
]

Protected_Header = {
  ? &(alg: 1) => int
  ? &(content_type: 3) => tstr / uint
  ? &(metamap: TBD) => metamap
}
~~~

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Security Considerations

TODO Security

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
