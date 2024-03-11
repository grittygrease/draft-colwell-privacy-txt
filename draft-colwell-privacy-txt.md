---
###
# Internet-Draft Markdown Template
#
# Rename this file from draft-todo-yourname-protocol.md to get started.
# Draft name format is "draft-<yourname>-<workgroup>-<name>.md".
#
# For initial setup, you only need to edit the first block of fields.
# Only "title" needs to be changed; delete "abbrev" if your title is short.
# Any other content can be edited, but be careful not to introduce errors.
# Some fields will be set automatically during setup if they are unchanged.
#
# Don't include "-00" or "-latest" in the filename.
# Labels in the form draft-<yourname>-<workgroup>-<name>-latest are used by
# the tools to refer to the current version; see "docname" for example.
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: "A File Format to Aid in Consumer Privacy Enforcement, Research, and Tools"
abbrev: "Privacy.txt File Format"
category: info

docname: draft-colwell-privacy-txt
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus:
v: 3
area:
workgroup:
keyword:
 - privacy
 - file format
venue:
  group: WG
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: USER/REPO
  latest: https://example.com/LATEST

author:
 -
    fullname: Nick Sullivan
    email: nicholas.sullivan+ietf@gmail.com

 -
    fullname: Louise Van der Peet
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

informative:


--- abstract

This proposal outlines a new file format called privacy.txt. It follows similar placement on a web server as robots.txt, security.txt, or ads.txt, in the / directory or /.well-known directory [1,2,3].

The file format adds structured data for three areas:
1. A machine parsable and complete privacy policy
2. Consumer actions under their privacy rights
3. Cookie disclosures


--- middle

# Introduction

Consumers in many parts of the world have extensive privacy rights under laws such as the GDPR and the CPRA. However without some formalization of a service's privacy policy, it is difficult or often intractable for:
- consumers to exercise those rights
- enforcement to verify compliance with laws and develop effective monitoring
- researchers and technologists to develop tools to allow greater adoption and success of privacy rights

Consumer data originally gets into the cloud by connections from consumer devices to web servers in the cloud. It should be possible to track the privacy policies applied to every byte of consumer data entering the cloud. Currently the association between a connection and the privacy policy is tenuous leading to the possibility of incorrect consumer data usage at the very source. This proposal fills that hole by nature of associating structured privacy data with every web server. Just like HTTPS security can be technically enforced, this proposal makes it possible to technically enforce privacy.

This proposal outlines a new file format called privacy.txt. It follows similar placement on a web server as robots.txt, security.txt, or ads.txt, in the / directory or /.well-known directory [1,2,3].

The file format adds structured data for three areas:
1. A machine parsable and complete privacy policy
2. Consumer actions under their privacy rights
3. Cookie disclosures

The file format is UTF8 text and lists `Field:Value`, one per line. Whitespace and lines that start with `#` are ignored.

## Machine parsable and complete privacy policy

It is currently difficult to associate a complete privacy policy text with a service for a number of reasons. First, even though it must be linked from the company webpage, there is not a canonical URL. Second, it is common for services to use client-side rendering, interactive elements, break out links for addendums, and server rules to prevent machine parsing/scraping. 

This file format uses two fields for the privacy policy. One or both can be used. 

Privacy-policy-text:URL

A complete privacy policy in a single UTF8 text file that can be downloaded by any user agent or machine tool. This must include all addendums in the text file. It must not include links. Information about contact and consumer actions are covered in this file format and do not need to be linked to in the policy text.

Privacy-policy:URL

If Privacy-policy-text is present, this can simply point to the existing privacy policy, in whatever form it currently exists. Otherwise, it must point to a machine parsable/scrapable static HTML file that contains the complete policy on a single page.


# Consumer actions under their privacy rights

This file format structures the consumer actions described in the privacy policy and commonly required by law.

Contact:mailto:EMAIL

An email contact for the privacy office must be given. Unless further Action-* fields are defined, this email must be able to handle consumer requests via email. Responses can ask for additional verification but should not require customer password or login.

Below a one-click URL refers to a URL that is able to process a request without requiring a customer password or login. The URL should take customer identification such as email and verify as necessary to complete the request.

Action-delete-account-and-data:mailto:EMAIL|URL

Email or one-click URL to process an account and data deletion request.

Action-delete-personal-data:mailto:EMAIL|URL

Email or one-click URL to process a personal data deletion request.

Action-opt-out-sharing:mailto:EMAIL|URL

Email or one-click URL to opt out of personal data sharing with third parties.

Action-shared-list:mailto:EMAIL|URL

Email or one-click URL to get a list of all third parties where personal data has been shared.

Action-opt-out-marketing:mailto:EMAIL|URL

Email or one-click URL to opt out of marketing.


# Cookie disclosures

TODO Cookie implementation guide


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

Following this file format makes it easier for consumers to take privacy actions, similar to one-click unsubscribe. Removing the barrier to actions makes it easier to make mistakes. It would be reasonable to allow some grace period of undo in case of a security incident.

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

Existing publication and implementation guide.

TODO acknowledge.
