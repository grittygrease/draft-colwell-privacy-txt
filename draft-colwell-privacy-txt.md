---
title: "A File Format to Aid in Consumer Privacy Enforcement, Research, and Tools"
abbrev: "Privacy.txt File Format"
category: info

docname: draft-colwell-privacy-txt-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date: 2024-05-21
consensus:
v: 3
area:
workgroup:
keyword:
 - privacy
 - file format
venue:
  group:
  type:
  mail: draft-colwell-privacy-txt@ietf.org
  arch:
  github: https://github.com/grittygrease/draft-colwell-privacy-txt
  latest: https://www.privacytxt.dev

author:
 -
    fullname: Nick Sullivan
    organization: Cryptography Consulting LLC
    email: nicholas.sullivan+ietf@gmail.com
 -
    fullname: Louise van der Peet
    organization: TU Delft
    email: L.VanderPeet@tudelft.nl
 -
    fullname: Georgios Smaragdakis
    organization: TU Delft
    email: g.smaragdakis@tudelft.nl
 -
    fullname: Brien Colwell
    organization: BringYour, Inc.
    email: brien@bringyour.com

normative:
  RFC6068:
  RFC8415:
  RFC7231:
  RFC3629:
  RFC7230:
  RFC2046:
  ISO3166:
    title: "Codes for the representation of names of countries and their subdivisions - Part 1: Country codes"
    date: 2020
    target: https://www.iso.org/standard/72482.html
    author:
      - org: International Organization for Standardization (ISO)
  ISO639:
    title: "Code for individual languages and language groups"
    date: 2023
    target: https://www.iso.org/standard/74575.html
    author:
      - org: International Organization for Standardization (ISO)

informative:
  RFC9309:
  RFC9116:
  ADS-TXT:
    title: "Ads.txt 1.1 Specification"
    author:
      - org: IAB Tech Lab
    date: April 2022
    target: https://iabtechlab.com/wp-content/uploads/2022/04/Ads.txt-1.1.pdf
  CAN-SPAM:
    title: "CAN-SPAM Act: A Compliance Guide for Business"
    author:
      - org: Federal Communications Commission (FCC)
    target: https://www.fcc.gov/general/can-spam
  GUIDE:
    author:
      - name: "Louise van der Peet"
    title: "Increasing privacy-related transparency on the web using a self-disclosing standard"
    date: 2023
    target: http://resolver.tudelft.nl/uuid:64b40236-787d-4ae0-8700-60cfe1598bfe
  MANAGERS:
    title: "Consent Manager List"
    target: https://github.com/privacy-txt/privacy-txt-tools/blob/master/data-collector/data/consent_managers.json

--- abstract

This proposal outlines a new file format called privacy.txt. It follows similar placement on a web server as robots.txt {{RFC9309}}, security.txt {{RFC9116}}, or ads.txt {{ADS-TXT}}, in the / directory or /.well-known directory.

The file format adds structured data for three areas:
1. A machine parsable and complete privacy policy
2. Consumer actions under their privacy rights
3. Cookie disclosures

--- middle

# Introduction

Consumers in many parts of the world have extensive privacy rights under laws such as the GDPR (https://gdpr.eu/tag/gdpr/), the CCPA (https://oag.ca.gov/privacy/ccpa). However, without some formalization of a service's privacy policy, it is difficult or often intractable for consumers to exercise those rights; enforcement to verify compliance with laws and develop effective monitoring; and researchers and technologists to develop tools to allow greater adoption and success of privacy practices.

Consumer data originally gets into the cloud by connections from consumer devices to web servers in the cloud. To be able to audit and technically enforce privacy it must be possible to track the privacy policies applied to every byte of consumer data entering the cloud. However, currently the association between a web request and the privacy policy is tenuous, leading to the possibility of incorrect or unverifiable consumer data usage at the very source. This proposal fills that hole by associating structured privacy data with every web server. Just like HTTPS security can be technically enforced, this proposal makes it possible to technically enforce privacy by verifying that the structured privacy information exists and is in good standing before sending data to the server.

This proposal outlines a new file format called privacy.txt. The file format adds structured data for three areas:
1. A machine-parsable and complete privacy policy
2. Consumer actions under their privacy rights
3. Cookie disclosures

## General file format

The file format is UTF8 text {{!RFC3629}} and lists `Field:Value`, one per line. `\n` is used a line break. Excess whitespace and lines that start with `#` are ignored. All field are NOT case sensitive unless mentioned otherwise. Each field MUST appear on its own line.  Unless otherwise specified by the field definition, multiple values MUST NOT be chained together for a single field. Unless otherwise indicated in a definition of a particular field, a field MAY appear multiple times.

Implementors should be aware that some of the fields may contain URIs using percent-encoding (as per Section 2.1 of {{!RFC3986}}) and mailto URIs (as per {{!RFC6068}}).


## File placement

For web-based services, organizations MUST place the "privacy.txt" file under the "/.well-known/" path, e.g., https://example.com/.well-known/privacy.txt as per {{!RFC8615}} of a domain name or IP address. For legacy compatibility, a "privacy.txt" file might be placed at the top-level path or redirect (as per Section 6.4 of {{!RFC7231}}) to the "privacy.txt" file under the "/.well-known/" path. If a "privacy.txt" file is present in both locations, the one in the "/.well-known/" path MUST be used.

The file MUST be accessed via HTTP 1.0 or a higher version, and the file access MUST use the "https" scheme (as per Section 2.7.2 of {{!RFC7230}}). It MUST have a Content-Type of "text/plain" with the default charset parameter set to "utf-8" (as per Section 4.1.3 of {{!RFC2046}}).

Retrieval of "privacy.txt" files and resources indicated within such files may result in a redirect (as per Section 6.4 of {{RFC7231}}).


## Valid value formats

### NAME

A string of maximum 50 characters. The string can contain any US-ASCII characters except for: control characters (ASCII characters 0 up to 31 and ASCII character 127) or separator characters (space, tab and the characters: ( ) < > @ , ; : \ " / [ ] ? = { }). This field is case sensitive.

### COUNTRY_CODE

The country code MUST follow 2-letter [ISO 3166-1](https://www.iso.org/standard/72482.html).

### LANGUAGE_CODE

The language code MUST follow 2-letter [ISO 639-1](https://www.iso.org/standard/74575.html).
 
### CONSENT_PRESENT

A boolean attribute, using 0 or 1 representing false (0) and true (1), whether a consent banner is present.

### CONSENT_PLATFORM

String attribute can be set to:
1. Label of consent platforms according to {{MANAGERS}} (extendable)
2. `non-specific custom` or any identifying name if it is a custom banner
3. `non-detected` when there is no banner

### DURATION

An integer attribute of the duration between 0 and the maximum duration. -1 can be used for session cookies.

## A machine parsable and complete privacy policy

It is currently difficult to associate a complete privacy policy text with a service for a number of reasons. First, even though it must be linked from the company webpage, there is not a canonical URL. Second, it is common for services to use client-side rendering, interactive elements, break out links for addendums, and server rules to prevent machine parsing/scraping.

This file format proposes two fields for the privacy policy. One or both can be used, depending on the policy format.

Both privacy policy fields can be used for multi-language support, the default entry is the default language of the privacy policy.

### Issuer information

Mandatory, single entry

`Entity: NAME`
`Entity-country: COUNTRY_CODE`

The legal name of the entity issuing the privacy policy.

The current and historical mapping of hostname to entity can be used as a canonical key to associate privacy reputation or enforcement actions similar to a certificate authority. This proposal does not outline what a privacy authority would look like.

### Privacy policy text

Optional, single entry

`Privacy-policy-text: URL`

Multi-language support:
 `Privacy-policy-text-[LANGUAGE_CODE]: URL`

A complete privacy policy in a single UTF-8 text file that can be downloaded by any user agent or machine tool. This MUST include all addendums in the text file. It MUST NOT include links. Information about contact and consumer actions are covered in this file format and do not need to be linked to in the policy text.

### Privacy policy URL

Mandatory, single entry

`Privacy-policy: URL`

Multi-language support:

`Privacy-policy-[LANGUAGE_CODE]: URL`

If Privacy-policy-text is present, this can simply point to the existing privacy policy, in whatever form it currently exists. Otherwise, it must point to a machine parsable/scrapable static HTML file that contains the complete policy on a single page.


## Consumer actions under their privacy rights

This file format proposed fields to structure the consumer actions described in the privacy policy and commonly required by law. Currently it is difficult to get even an email that can service privacy requests from many top-100 site privacy policies. There is currently no law about how easy it should be to take privacy actions, similar to the US CAN-SPAM Act {{CAN-SPAM}}, which led to an industry standard one-click link for marketing emails. The spirit of these fields is similar, to make it as easy as possible for a consumer to exercise their privacy rights.

In this section, a *one-click URL* refers to a URL that can process a request without requiring a customer password or login. The URL should take customer identification such as email and verify as necessary to complete the request.

It is allowed to have multiple conforming Action-* values for the same action.

An API standard to make privacy actions more toolable is not covered in this proposal. This proposal could be extended in the future to allow some well-defined API actions given there is at least one other non-assisted option available.

### Privacy contact email

Mandatory, single entry

`Contact: mailto:EMAIL`

An email contact for the privacy office must be given. This email must be able to handle consumer requests via email where there is not an applicable `Action-*` field for the request. Responses can ask for additional verification but should not require customer password or login. If `Action-*` fields are defined for all applicable consumer requests, this email does not need to handle any requests. This proposal imagines companies would build self-service one-click URLs for all consumer actions as the most scalable outcome.

### Actions

#### Account and data deletion
Optional, single entry

`Action-delete-account-and-data: mailto:EMAIL|URL`

Email or one-click URL to process an account and data deletion request.

#### Personal data deletion
Optional, single entry

`Action-delete-personal-data: mailto:EMAIL|URL`

Email or one-click URL to process a personal data deletion request.

#### Opt out of third-party data sharing
Optional, single entry

`Action-opt-out-sharing:mailto: EMAIL|URL`

Email or one-click URL to opt out of personal data sharing with third parties.

#### List of third parties
Optional, single entry

`Action-shared-list:mailto: EMAIL|URL`

Email or one-click URL to get a list of all third parties where personal data has been shared.

#### Opt out of marketing
Optional, single entry

`Action-opt-out-marketing: mailto:EMAIL|URL`

Email or one-click URL to opt out of marketing.


## Cookie disclosures

Common privacy laws call for transparency in cookie storage. In order to audit and enforce transparency, this file format proposes fields that describe the cookies used by a web site, following a previously published format {{GUIDE}}. A web browser could technically enforce this declaration by refusing access to undeclared cookies.

### Consent banner
Optional, single entry

`Banner: CONSENT_PRESENT`

A boolean attribute, using 0 or 1 representing false (0) and true (1), whether a consent banner is present.

### Consent platform name
Optional, single entry

`Consent-platform: CONSENT_PLATFORM`

The consent management platform name, which can be set to `non-specific-custom` or any identifying name if it is a custom banner, or set to `none detected` when there is not banner.

### Cookies
Optional, multiple entries

`Cookie: 1_NAME, 2_DOMAIN, 3_DURATION, 4_PARTY, 5_OPTIONAL, 6_HTTPONLY, 7_SECURE`

The field values are given as a complete septuple with each field defined by the sections, taken from {{GUIDE}}. From these fields, the most important cookie attributes related to privacy and compliance can be derived.


#### 1_NAME Cookie name

`NAME`

The name of the cookie. This identifies which cookie is set. The website uses this together with the value to identify the cookie.

#### 2_DOMAIN Domain name of the cookie

`URL`

The domain attribute of a cookie specifies which domain may receive the cookie. If this is the same as the host domain, that means it is a first party cookie.

#### 3_DURATION Duration of the cookie

`DURATION`

The duration attribute contains the storage limit of the cookie. This is in the form of the amount of seconds the cookies will remain on the user's device before it is expired and deleted.

#### 4_PARTY First or Third party cookie

This is a boolean attribute, using 0 or 1 representing false (0) and true (1), that indicates whether the cookie is a third party cookie. Thus means that the target domain is different from the host domain. It is placed on the website by someone other than the owner and collects data for that third party.

#### 5_OPTIONAL Optional status

This is a boolean attribute, using 0 or 1 representing false (0) and true (1), which indicates whether this is an options cookie or not. Optional cookies can be refused by the user, using the consent banner. When cookies are not optional they will always be placed on the user's device when they access the website, with or without consent.

#### 6_HTTPONLY HTTPonly status

This is a boolean attribute, using 0 or 1 representing false (0) and true (1), which indicates whether the httpOnly flag is set. This means that the cookie can only be transferred via HTTP, and therefor the cookie can only be accessed by the current server. This helps mitigate clientside scripts accessing the cookie data.

#### 7_SECURE Secure status

This is a boolean attribute, using 0 or 1 representing false (0) and true (1), which indicates whether the secure flag is set on the cookie. The secure flag causes the browser to only send the cookie over encrypted channels, therefor securing the communication between the user's device and the server.

# Other records

Other records that are not part of the privacy.txt protocol may be included. For example, a regulation-specific record like `action-fcra-freeze` could be added to comply to FCRA obligations.

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

Following this file format makes it easier for consumers to take privacy actions, similar to one-click unsubscribe. Removing the barrier to actions makes it easier to make mistakes. It would be reasonable to allow some grace period of undo in case of a security incident.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}
