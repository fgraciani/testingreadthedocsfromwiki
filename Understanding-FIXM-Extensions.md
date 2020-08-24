`GENERAL GUIDANCE`

# What is a FIXM extension?

An extension designates a supplement to FIXM that supports additional
(commonly local or regional) requirements from a particular organisation
or community of interest. An extension may supplement FIXM Core by
defining additional flight data structures exchanged locally or
regionally, and/or may supplement an existing Application Library by
defining additional messaging data structure exchanged locally or
regionally.

# What is a valid use of an extension?

A number of rules are established in order to ensure that extensions are
not developed as a replacement of FIXM Core or a subset thereof.

The requirements on FIXM extensions are provided below. They are equally
applicable to verified and non-verified extensions, but are enforceable
only for verified extension. Non-verified extensions satisfying the
requirements below will be recognised as a valid usage of the FIXM
extension mechanism.

## Requirement on extension design

| **To qualify for a valid FIXM extension, an extension shall be designed in accordance with the modelling principles described in APPENDIX A.** |
|:---|

`RATIONALE` The successful development of an extension, and its successful integration with the FIXM core packages, requires rules on extension design to be followed consistently by all implementers.

`HOW TO CHECK THIS` Checking that an extension satisfies this requirement cannot be automated and requires manual analysis of the extension content by the FIXM community. As a general principle, extensions to FIXM core that are proposed for online publication on the FIXM web site should be checked against this requirement.

## Requirement on extension content

| **To qualify as a valid FIXM extension, an extension shall never contain a model element that would redefine, or supersede, a model element that is already defined in FIXM Core.**|
|:---|

`RATIONALE` FIXM core is an information exchange model capturing flight information that is globally harmonised. Redefining or superseding the FIXM core content in an extension would amount to diverging from this globally harmonised content and would go against the fundamental harmonisation objectives of FF-ICE and FIXM. |

`HOW TO CHECK THIS` Checking that an extension satisfies this requirement cannot be automated and requires manual analysis of the extension content by the FIXM community. As a general principle, extensions to FIXM core that are proposed for online publication on the FIXM web site should be checked against this requirement.       |

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

> TODO


`EXAMPLE` <img src="./media/nok.png" style="width:0.25in;height:0.25in" />


<img src="media/wrong_extension_example.png" style="width:4.70091in;height:2.96439in" />

*Figure: Example of FIXM extension NOT satisfying the requirement on extension content*

This example features a fictitious extension to FIXM Core which models
one class entitled “WrongFlight” (in blue on the diagram). This class
defines a property named “gufi” that is typed using CharacterString. The
extension essentially redefines the “gufi” property from the FIXM core
model element “Flight” and loosens its format, allowing any type of
character string to be populated. This is an example of a FIXM extension
redefining content from FIXM Core. It does NOT qualify as valid usage of
the FIXM extension mechanism.

# How to build an extension?

The FIXM extension mechanism distributes class-specific extension hooks
throughout the model that implementers can leverage to define their
specific data structures.

<img src="./media/aircraftType_and_extension_hook.png"/>

The key benefits of the approach are the following:

1.  ability to allow Extension validation

2.  multiple co-existing Extensions

3.  co-location of Extension data with the Core data it extends

4.  ability to easily remove extensions and pare down the model

This permissive approach enables FIXM users to enrich the core FIXM
datasets with as many information elements as necessary, as required by
the applicable implementation context.

APPENDIX A provides a rulebook and detailed guidance for creating
extensions.