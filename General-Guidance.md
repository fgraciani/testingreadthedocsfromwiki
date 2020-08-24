# General Guidance on FIXM implementation

## Target audience

This chapter provides general guidance for understanding the main FIXM
components, outlines general requirements for valid FIXM core and FIXM
extensions usage and describes the general rules (encoding rules, data
plausibility rules, rules for absent data…) that are always applicable
whatever the implementation context. It therefore targets all FIXM
implementers.

## Understanding FIXM Core, the Application Libraries and the Extensions

### FIXM Core

#### What is it?

**FIXM Core** provides globally harmonized flight data structures that
can be exchanged in various contexts. The main context for the use of
**FIXM Core** is ICAO FF-ICE. Therefore, **FIXM Core** currently
captures the flight data structures that are identified in the ICAO
FF-ICE Implementation Guidance Manual 0.91. Only flight data structures
that are globally applicable qualify for FIXM Core. Flight data
structures that are local or regional in nature do not qualify for
**FIXM Core**. An **Extensions** mechanism is implemented so that **FIXM
Core** can be extended in order to cover these local or regional data
structures, as appropriate.

**FIXM Core** exists as a standard for exchanging flight data rather
than as a set of pre-defined messages, allowing flexible exchanges
between users rather than enforcing rigid communication patterns.
However, once a given exchange is well-defined, it is useful to be able
to enforce syntax and content validation checks to ensure the data being
exchanged is of high quality. This is addressed by **Application
Libraries**.

#### What is a valid FIXM Core usage?

The general requirements for a valid **FIXM Core** usage are the
following:

##### Requirement on data structure

| **To qualify as valid usage of FIXM Core, the flight-related content of a given message, or relevant part thereof, shall be syntactically valid against the FIXM Core XML Schemas.**|
|:---|

`RATIONALE` The valid usage of FIXM Core implies that the flight-related content of a message exchanged between two parties is valid against the FIXM Core XML Schemas. If a message includes additional information not in scope of FIXM Core, it must be structured so that its relevant part is valid against the FIXM Core XML Schemas.

`IMPORTANT NOTE` Being syntactically valid against the FIXM Core XML Schemas implies the FIXM Core hierarchy is respected. FIXM Core is not expected to be used only as a library of flight datatypes.

`HOW TO CHECK THIS` The content of a message, or relevant part thereof, validates without error against the FIXM Core XML schemas when tested / parsed by XML validation tools.


`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:aerodrome>
   <fb:locationIndicator>EBBR</fb:locationIndicator>
</fx:aerodrome>
```

This example displays an aerodrome reference involving a four-letter
ICAO location indicator. It complies with the structural rules for
aerodrome references defined by the FIXM Core XML schemas.


`EXAMPLE` <img src="./media/nok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:aerodrome>
   <fb:locationIndicator>BRU</fb:locationIndicator>
</fx:aerodrome>
```

This example displays an aerodrome reference based on property
locationIndicator. The value “BRU” does not respect the pattern
\[A-Z\]{4} enforced by FIXM for property locationIndicator. This example
does NOT comply with the structural rules for aerodrome references
defined by the FIXM XML schemas and does not qualify as valid FIXM
usage.


`EXAMPLE` <img src="./media/nok.png" style="width:0.25in;height:0.25in" />

The example below features a valid XML schema that defines a Flight
Identification structure comprising the departure & arrival aerodrome
references, the aircraft identification and the estimated off-block
time. It also features an example XML sample that is valid against this
schema.

```xml
<xs:schema xmlns:wrong="fixm_as_library_of_types" xmlns:fx="http://www.fixm.aero/flight/4.2" xmlns:fb="http://www.fixm.aero/base/4.2" […] >  
[…]
   <xs:element name="FlightIdentification" type="wrong:FlightIdentificationType"/> 
   <xs:complexType name="FlightIdentificationType"> 
      <xs:sequence> 
         <xs:element name="departureAerodrome" type="fb:AerodromeReferenceType"/> 
         <xs:element name="arrivalAerodrome" type="fb:AerodromeReferenceType"/> 
         <xs:element name="ACID" type="fb:AircraftIdentificationType"/> 
         <xs:element name="EOBT" type="fb:TimeType"/> 
      </xs:sequence> 
   </xs:complexType> 
</xs:schema>
```
<img src="./media/Double_arrow_symbol_-_blue.png" style="width:0.50in;height:0.70in" />

```xml
<wrong:FlightIdentification xmlns:wrong=[…] xmlns:fb="http://www.fixm.aero/base/4.2" xmlns:xs="http://www.w3.org/2001/XMLSchema-instance" xs:schemaLocation=[…]>
   <wrong:departureAerodrome>
      <fb:name>LES BARAQUES</fb:name>
   </wrong:departureAerodrome>
   <wrong:arrivalAerodrome>
      <fb:name>NORTHFALL MEADOW</fb:name>
   </wrong:arrivalAerodrome>
   <wrong:ACID>BLXI</wrong:ACID>
   <wrong:EOBT>1909-07-25T04:41:00.000Z</wrong:EOBT>
</wrong:FlightIdentification>
<!-- https://en.wikipedia.org/wiki/Louis\_Bl%C3%A9riot\#1909\_Channel\_crossing -->
```


The example schema above is not FIXM Core and is not a FIXM extension.
It is a fictitious, standalone XML schema that defines its own hierarchy
of elements, but which reuses types from the core FIXM XML schemas for
typing these elements. The reuse of FIXM datatypes is highlighted in
blue in the schema description.

This example illustrates the reuse of FIXM Core as a library of
datatypes. While this practice is technically feasible and produces
valid schemas, it is not considered a valid FIXM Core usage because it
breaks the hierarchy of properties defined by FIXM Core. An information
service relying on such an implementation practice would fail to satisfy
the FIXM Core requirement on data structure.

##### Requirement on data correctness

| **To qualify as valid usage of FIXM core, the flight-related content of a given message, or relevant part thereof, shall satisfy the minimum set of rules addressing data plausibility and consistency.** |
|:---|

`RATIONALE` The flight-related content of a message being syntactically correct and complete may still not make sense from an operational or plausibility perspective. Additional business rules are required to check the correctness of the encoded information, such as the consistency between model elements.

`HOW TO CHECK THIS` The content of a message, or the relevant part thereof, validates without error against the applicable business rules addressing data correctness. Chapter 2.4.13 lists business rules addressing data correctness which are always applicable whatever the context of the exchange. Additional business rules addressing data correctness may exist which are specific to particular use-cases.


`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:verticalRange>
   <fb:lowerBound>
      <fb:flightLevel uom="FL">240</fb:flightLevel>
   </fb:lowerBound>
   <fb:upperBound>
      <fb:flightLevel uom="FL">250</fb:flightLevel>
   </fb:upperBound>
</fx:verticalRange>
```

This example shows the FIXM encoding of vertical range \[FL240;FL250\].
It satisfies the basic data plausibility/correctness rule “*The
lowerBound shall always be lower than the upperBound*” that is
identified in Chapter 2.4.13. It qualifies as valid FIXM core usage.



`EXAMPLE` <img src="./media/nok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:aircraft>
   <fx:aircraftType>
      <fx:numberOfAircraft>2</fx:numberOfAircraft>
	  <fx:type>
         <fx:icaoAircraftTypeDesignator>MIR2</fx:icaoAircraftTypeDesignator>
      </fx:type>
   </fx:aircraftType>
   <fx:aircraftType>
      <fx:numberOfAircraft>1</fx:numberOfAircraft>
      <fx:type>
         <fx:icaoAircraftTypeDesignator>RFAL</fx:icaoAircraftTypeDesignator>
      </fx:type>
   </fx:aircraftType>
   <fx:formationCount>2</fx:formationCount>
</fx:aircraft>
```

This example represents a description of a fictitious formation of
military aircraft composed of two Mirages 2000 and one Rafale which
altogether constitute a single (formation) flight. This example is valid
from a data structure point of view (it validates against the FIXM core
XML schemas) but is not correct in so far as the sum of all
AircraftType.numberOfAircraft properties does not match
Aircraft.formationCount, which breaks a rule from Chapter 2.4.13. This
example does not qualify as valid FIXM core usage.

### Application Libraries

#### What is it?

An **Application Library** is a FIXM component that addresses the use of
FIXM Core in a given context. It can be of global, regional or local
applicability, depending on the context. An **Application Library**
essentially provides context-specific **‘message data structures’** and
**‘message templates’** which enables harmonized representation of the
FIXM-based messages exchanged using SWIM information services, as
outlined in the figure below.

<img src="./media/message_structure_fixm_application_role.png"/>

*Figure: General structure of a message and role of an Application Library*

An **Application Library** captures messaging related data elements and
reuses and restricts relevant subsets of the FIXM Core data structures.
FIXM Core is independent and does not require an update when changes in
an application library occur.

An **Application Library** may also leverage **Extensions**, as illustrated on the picture below:

<img src="./media/fixm_application_with_extension.png" style="width:1.66806in;height:2.1125in" />


An example of an Application Library is the **FF-ICE Application
Library** developed and released by the FIXM CCB. This library addresses
the use of FIXM core in the specific context of FF-ICE. It provides
harmonized FF-ICE Message data structure (e.g. data structures for
representing the FF-ICE Filing Status, the FF-ICE Planning Status etc.)
and the FF-ICE message templates (e.g. the template for the FF-ICE Filed
Flight Plan Message, the template for the FF-ICE Flight Cancellation
Message etc.), in line with the FF-ICE Implementation Guidance Manual.

More details about this FF-ICE Application Library can be found in
Chapter 3.2 .

#### Message Data Structures

Message Data structures designate at high level the data structures that
are necessary for understanding the meaning and purpose of the
information that is exchanged in a given context. They commonly include
message identifiers and timestamps, codes identifying business types of
messages, and any context-specific data that qualify the associated
message interactions[2].

Examples of message data structures can be found in the FF-ICE
Implementation Guidance Manual. The Figure below shows the message data
structures associated with the FF-ICE Flight Cancellation Message.

<img src="./media/ffice_message_data_example.png" style="width:2.36522in;height:3.62891in" />

*Figure 2: Example of Message Data structures from FF-ICE*

#### Message Templates

A message template is a more restrictive subset of message and flight
data structures that is relevant to a given information exchange. In
SWIM terms, a message template provides guidance for formatting a given
information service payload.

By removing unused fields, adjusting multiplicities, and adding or
further limiting pattern constraints, a template can tailor the broad
standard represented by FIXM to reflect the content requirements of a
particular message exchange. Templates offer message-specific guidance
and validation rules while remaining entirely compliant with the broader
FIXM structures.

A list of benefits for employing templates is detailed below.

| **Benefit of templates**     | **Without templates**                                                                                                                                                                        | **With templates**                                                                                                             |
|------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| Reduced Development Overhead | Increased development overhead as each user must independently interpret how message content requirements should be represented in FIXM format.                                              | Tailored schemas reduce development overhead by providing additional guidance for creating messages with a FIXM-based content. |
| Consistent Message Structure | Individual interpretations of requirements could lead to inconsistent message content implementation across users.                                                                           | Making dedicated implementation templates available to all users should improve implementation consistency.                    |
| Improved XML Validation      | XML-based validation limited to data syntax checking with no guidance for required vs. optional or allowed vs. not allowed content (failing to fully leverage a major benefit of using XML). | XML-based validation enforces both syntax and content completeness rules (fully leveraging benefits of XML-based validation).  |

The use of message templates therefore improves interoperability, data
quality, and ease and cost of development for any exchange they are
applied to. They provide FIXM users with guidance and structure while at
the same time allowing FIXM to remain open and flexible.

**XML representation of FIXM-based Message Templates**

The XML representation of FIXM-based Message templates is currently
achieved by restricting complex types defined by FIXM. Restricting
complex types is a standard-based approach for removing unwanted
elements and/or attributes and to apply tighter restraints to
multiplicities, patterns, and facets. Complex type restrictions also
provide built-in validation: if the restriction is not correctly formed
in relation to the parent type then the resulting schemas will not
validate.

**Benefits of XSD restrictions**

-   XSD restrictions are explicit: using an XSD schema with restrictions
    means using the rules of the base XSD schema plus additional rules
    that are explicitly declared;

-   XSD restrictions provide some built-in validation for quality
    assurance

-   XSD restrictions represent a natural use of the XSD standard;

-   XSD restrictions deliver benefits in terms of model development and
    maintenance. [3]

**Potential shortcomings of XSD restrictions**

The online literature about XML schema design generally considers that
the restrictions of XSD complex types are the most difficult and
therefore the least supported part of the XML schema specification.
Implementers experiencing issues with the FIXM templates are invited to
report their problems to the FIXM community, with details about the
development environment being used. Alternatives to XSD restrictions may
be then considered, as appropriate (see next section).

**XSD Profiles as a potential alternative to XSD Restrictions**

An XSD profile would represent a reduced, further restricted subset of
the original model. This approach is very similar to using restrictions
but accomplishes the task by directly creating smaller, parallel models
of the adjusted packages rather than producing them via a restriction.
The figure below illustrates at high-level the differences.

<img src="./media/xsd_restriction_vs_xsd_profile.png" style="width:5.14179in;height:3.52252in" />

*Figure: XSD Restriction vs XSD Profile*

XSD profiles would not restrict the types from the base reference and
would not bring any additional complexity. They could therefore be
processed by marshalling tools in a smoother way compared to XSD
restrictions.

XSD profiles may be therefore developed as an alternative to XSD
restrictions for representing FIXM-based message templates.

#### How to build an application library?

APPENDIX B provides detailed guidance for creating application
libraries.

### Extensions

#### What is it?

An extension designates a supplement to FIXM that supports additional
(commonly local or regional) requirements from a particular organisation
or community of interest. An extension may supplement FIXM Core by
defining additional flight data structures exchanged locally or
regionally, and/or may supplement an existing Application Library by
defining additional messaging data structure exchanged locally or
regionally.

#### What is a valid use of an extension?

A number of rules are established in order to ensure that extensions are
not developed as a replacement of FIXM Core or a subset thereof.

The requirements on FIXM extensions are provided below. They are equally
applicable to verified and non-verified extensions, but are enforceable
only for verified extension. Non-verified extensions satisfying the
requirements below will be recognised as a valid usage of the FIXM
extension mechanism.

##### Requirement on extension design

| **To qualify for a valid FIXM extension, an extension shall be designed in accordance with the modelling principles described in APPENDIX A.** |
|:---|

`RATIONALE` The successful development of an extension, and its successful integration with the FIXM core packages, requires rules on extension design to be followed consistently by all implementers.

`HOW TO CHECK THIS` Checking that an extension satisfies this requirement cannot be automated and requires manual analysis of the extension content by the FIXM community. As a general principle, extensions to FIXM core that are proposed for online publication on the FIXM web site should be checked against this requirement.

##### Requirement on extension content

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

#### How to build an extension?

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

#### Ignoring extension data 

Consumers of FIXM information may not need, and/or may not be able to
process and interpret extension data supplementing a core FIXM dataset.

Using XSLTs is one approach for removing unwanted Extension data (known
or unknown) from a FIXM XML dataset, as appropriate. An example of an
XSLT that removes all Extension content is provided below:

```xml
<?xml version="1.0" encoding="UTF-8"?> 
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform"> 
   <xsl:output method="xml" version="1.0" encoding="UTF-8" indent="yes"/> 
   <xsl:template match="@\*\|node()"> 
      <xsl:copy> 
         <xsl:apply-templates select="@\*\|node()"/> 
      </xsl:copy> 
   </xsl:template> 
   <xsl:template match="\*:extension"/> 
</xsl:stylesheet>
```

Tested Development Environments
-------------------------------

> TODO

General encoding rules
----------------------

### Date/Time Specification

`FIXM TYPE` [fb:TimeType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_TimeType.html#LinkA6)

FIXM requires times to be expressed in UTC.

A constraint is placed on class *Base.Types.Time*, the class used to
represent all date/time values in FIXM, imposes the use of the trailing
character ‘z’ to indicate UTC, in line with the W3C XSD specification.

`EXAMPLE`
20th July 1969 at 20:18UTC is expressed as 1969-07-20T20:18:00.000Z

**Note to implementers**

The mapping of the XSD type dateTime to native structures in various
development contexts is not always 1-1 and may exhibit a wide variety of
difficulties depending on the tooling and runtime context. In
particular, the trailing character ‘z’ indicating UTC may actually be
stripped/omitted, leading to FIXM times being interpreted as local times
instead of UTC times by some applications. FIXM implementers are
therefore invited to crosscheck that their systems correctly interpret
FIXM times as UTC time.

### Geographical positions

`FIXM TYPE` [fb:GeographicalPositionType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_GeographicalPositionType.html#Link14)

<img src="./media/GeographicalPositionType.png">

FIXM captures the concept of Geographical Position as defined by ICAO
Annex 15.

> **Position (geographical).** Set of coordinates (latitude and longitude)
> referenced to the mathematical reference ellipsoid which define the
> position of a point on the surface of the Earth.*

This model element maps to the ISO 19107 “Point” construct, defined as a
single location given by a direct position.


A geographic location consists of a co-ordinate reference system and
geographic co-ordinates.

#### Co-ordinate reference system

ICAO Annex 11 chapter 2.29.1 states that World Geodetic System — 1984
(WGS-84) shall be used as the horizontal (geodetic) reference system for
air navigation. The Coordinate Reference System reference is critical
for the correct encoding and processing of FIXM positions*. This is
because a CRS not only indicates the geodetic datum and ellipsoid for
which point coordinates are expressed but also the order of the
coordinate axes in which coordinate values are provided, e.g. latitude
before longitude – which is an important convention for the aviation
domain.* \[copied from [OGC
12-028r1](https://portal.opengeospatial.org/files/?artifact_id=62061)\]
The EPSG:4326 CRS is the recommended choice for AIXM 5.1 data sets that
use the WGS-84 reference datum.

FIXM implements a fixed co-ordinate reference system:
“urn:ogc:def:crs:EPSG::4326”.

#### Geographic co-ordinates

The EPSG:4326 CRS has latitude as the primary axis, which indicates that
**the order of the values in the fb:pos element is** **first latitude**,
**second longitude**. This ordering convention is the one applied to the
aviation domain.

The co-ordinates are represented in FIXM by a two-valued sequence[4],
the first being the latitude and the second being the longitude, each of
which is a floating point number (the decimal value in degrees). The
direction is determined by the sign of the value, as specified in the
next table.

| Sign     | Latitude | Longitude |
|:---------|:---------|:----------|
| Positive | N        | E         |
| Negative | S        | W         |

Note the latitude and longitude values are encoded as double in FIXM.
Imposition of range restriction (-90<=latitude<=90, -180<=longitude<=180)
does not appear in the model since different elements of the sequence of
values have different constraints.

#### Examples

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:position srsName="urn:ogc:def:crs:EPSG::4326">
   <fb:pos>59.0 -30.0</fb:pos>
</fb:position>
```

In this example, number ‘59.0’ represents the latitude and number
‘-30.0’ represents the longitude.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:point4D srsName="urn:ogc:def:crs:EPSG::4326">
   <fb:pos>50.03330555555556  8.5704555555556</fb:pos>
</fx:point4D>
```


#### Miscellaneous

The W3C XML schema 1.0 specification defines three special values for
float/double: positive infinity, negative infinity and not-a-number. In
this context, a “pos” element expressed as 

<img src="./media/nok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:pos>INF -INF</fb:pos>
```
or 

<img src="./media/nok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:pos>NaN NaN</fb:pos>
```

would be syntactically correct; it would validate against the core FIXM XML
schemas. However, it would not represent any plausible location. The use
of these special values is therefore not accepted when exchanging
geographical positions in FIXM.

#### Why FIXM does not use the Geography Markup Language (GML)

FIXM does not adopt the GML standard for the representation of
geospatial data. The reasons for not adopting GML are the following:

-   Wherever a GML dependency is introduced, there would be a need for
    users to use the GML schemas and therefore to understand GML, which
    increases implementation costs.

-   The Flight Planning community is not traditionally familiar with
    geospatial concepts. The introduction of GML would become an
    important drawback for FIXM adoption in support of FF-ICE/R1.

-   Introducing a dependency on GML would make FIXM more difficult to
    implement particularly in certain environments. For instance, .NET
    technologies have been identified as *incompatible* with the GML
    usage.

### References to published aeronautical information

Describing the predicted movement of a flight commonly means indicating
which parts of the infrastructure (ATS routes, waypoints, radio
navigation aids etc.) are expected to be used by the flight. This is
enabled in FIXM by a set of “references” constructs. These references
are not flight-specific information; they pertain to the aeronautical
information domain.

Different formats exist for exchanging aeronautical information, whose
usages depend on specific implementation considerations and actual
context within the overall data chain. For instance:

-   AIXM is developed in order to enable the provision in digital format
    of the aeronautical information that is in the scope of Aeronautical
    Information Services (AIS). AIXM 5.1 covers both the content of
    Aeronautical Information Publications (AIP) and of the NOTAM
    information.

-   The ARINC 424 standard defines a format for navigation (and
    communication) information, including but not limited to, aerodrome,
    runway, navaid, airway and terminal approach procedure information
    that is exchanged between data suppliers and avionics vendors.

-   The Aerodrome Mapping Exchange Schema (AMXM XML Schema) is an
    exchange format specification for AMDB as standardized by and
    dedicated to the EUROCAE/RTCA Aeronautical Databases WG44/SC217. It
    is an XML Schema implementation of the DO-291C/ED-119C AMXM UML
    model.

**FIXM does not import any of these formats or profiles thereof.** FIXM
defines its own structures for referring to aeronautical information
that are **self-contained** but mappable to their AIXM/AMXM… equivalents
thanks to the reuse of a common semantics. FIXM also provides an
**optional** mechanism enabling these self-contained references to be
supplemented with, but not replaced by, **hypertext** **references** to
AIXM features.

The following sections provide guidance for correctly encoding these
references in FIXM.

#### Generic hypertext references

If an AIXM 5.1 feature exists that corresponds to the element being
referred to, an **optional** hypertext reference to that AIXM feature
may be provided. This reference shall be expressed in accordance with
chapter 3.4.1 (*Abstract references using UUID*) of the [AIXM feature
Identification and
Reference](http://www.aixm.aero/sites/aixm.aero/files/imce/AIXM51/aixm_feature_identification_and_reference-1.0.pdf)
document developed by the AIXM community.

This hypertext reference may be used - or ignored - by the receiving
system depending on its capabilities.

`EXAMPLE`

```xml
<fx:*\[FIXMelement\]* href="urn:uuid:81e47548-9f00-4970-b641-8ff8f99098a5">
```

Important note: FIXM does not import the W3C XML Linking Language
(xlink) v1.1 schemas in order to represent the hypertext references.
FIXM mimics the xlink Locator attribute named “href” but defines it
within the FIXM (Base) namespace[5].

#### References to Waypoints

`FIXM TYPE` [fb:DesignatedPointType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_DesignatedPointType.html#Link12)

<img src="./media/DesignatedPointType.png">

##### OPTION 1 - Minimum reference

As a minimum, the coded designator of waypoint shall be provided, as
published in the AIPs.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:designatedPoint>
   <fb:designator>TEMPO</fb:designator>
</fb:designatedPoint>
```

This is the minimum reference that SHALL be provided.


##### OPTION 2 - Unambiguous reference

*(OPTION 2 = OPTION 1 + supplementary position information)*

The coded designator of a waypoint is not always sufficient for
unambiguously referring to that element. The 5-letter coded designator
of a waypoint is supposed to be unique world-wide (according to ICAO
Annex 11) but is not in reality. There are at least 5%
duplicates/triplicates/even more…

FIXM adds an optional property 'position' which may be used as a
complement to the 'designator' information in order to remove any
ambiguity on the designator.

Important note: the combinations of fields \[designator + position\]
shall not be interpreted as a natural key uniquely identifying the
waypoint, in so far as producing and consuming systems/services may use
different aeronautical information sources with different degree of
precisions for lat/long, leading to small variations of the position
information. The supplementary fields shall be used for disambiguation
purposes only (i.e. plausibility checks based on position) in case of
duplicate/triplicate/…

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:designatedPoint>
   <fb:designator>TEMPO</fb:designator>
   <fb:position srsName="urn:ogc:def:crs:EPSG::4326">
      <fb:pos>56.84 -29.860000000000003</fb:pos>
   </fb:position>
</fb:designatedPoint>
```

##### OPTION 3 - Minimum reference with supplementary AIXM pointer

*(OPTION 3 = OPTION 1 + supplementary hypertext reference)*

Option 3 corresponds to Option 1 with an additional hypertext reference
as described in chapter Generic hypertext references.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:designatedPoint href="urn:uuid:81e47548-9f00-4970-b641-8ff8f99098a5">
   <fb:designator>TEMPO</fb:designator>
</fb:designatedPoint>
```

##### OPTION 4 - Complete reference

*(OPTION 4 = OPTION 2 + OPTION 3)*

Option 4 corresponds to the combination of Option 2 and Option 3. See
explanations above.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:designatedPoint href="urn:uuid:81e47548-9f00-4970-b641-8ff8f99098a5">
   <fb:designator>TEMPO</fb:designator>
   <fb:position srsName="urn:ogc:def:crs:EPSG::4326">
      <fb:pos>56.84 -29.860000000000003</fb:pos>
   </fb:position>
</fb:designatedPoint>
```


##### Note about the pattern constraint for waypoint designators

FIXM supports waypoint designators of 1 to 5 characters. This design is
intentional. In most cases, waypoints have a 5-letter designator, as
prescribed by ICAO. However, these designators might be shorter in some
particular cases. Examples of shorter waypoint designators could be
found in various sources:

-   In chapter 7 of the ARINC 424 specification. This chapter provides
    rules for forming the designators of waypoints in the absence of
    published designators. These rules can lead to designators of less
    than 5 characters. The following extracts from ARINC 424-19 provide
    some examples, which are highlighted in blue.

> Examples:
> 
> 3.0 NM from DOOTY should be expressed as <span style="color:blue">**3NM**</span>.
>
> 2.8 NM from CHASS should be expressed as <span style="color:blue">**28NM**</span>.
>
> 11.0 NM from BACUP should be expressed as <span style="color:blue">**NM11**</span>.
>
> 11.0 NM from BACUP should be expressed as <span style="color:blue">**NM11**</span>.
>
> 11.0 NM from KITTY should be expressed as <span style="color:blue">**NM138**</span>.

> |Fix Ident|Fix Name|
> |:--|:--|
> |<span style="color:blue">**AB13**</span>|AB180013 AB 180.3 degrees 12.8|

-   In the [US FAA’s publication of Instrument Flight Procedures
    encodings in ARINC 424-18
    format](https://www.faa.gov/air_traffic/flight_info/aeronav/digital_products/cifp/download/).
    For instance:

    -   Localizer only approach on RWY06 at KFOD

> SUSAP KFODK3FL06 AFOD 010FOD K3D 0V IF 18000 0 NS 854081209
>
> SUSAP KFODK3FL06 AFOD 020<span style="color:blue">**FF06**</span> K3PC0E TF + 02800 0 NS 854091209
>
> SUSAP KFODK3FL06 AFOD 030<span style="color:blue">**FF06**</span> K3PC0EE AR PI IFODK3 2430006119800100PI + 02800 0 NS 854101209
>
> SUSAP KFODK3FL06 AHIMSU 010HIMSUK3PC0E A IF FOD K3 15000140 D 18000 0 NS 854111310
>
> SUSAP KFODK3FL06 AHIMSU 020SHRLAK3PC0EE BR AF FOD K3 216501401500 D + 02800 0 NS 854121310
>
> SUSAP KFODK3FL06 ALESFI 010LESFIK3PC0E A IF FOD K3 25400140 D 18000 0 NS 854131310
>
> SUSAP KFODK3FL06 ALESFI 020SHRLAK3PC0EE BL AF FOD K3 216501402540 D + 02800 0 NS 854141310
>
> SUSAP KFODK3FL06 L 010SHRLAK3PC0E I IF IFODK3 24300163 PI + 02800 18000 0 NS 854151310
>
> SUSAP KFODK3FL06 L 020<span style="color:blue">**FF06**</span> K3PC0E F CF IFODK3 2430006106300101PI + 02800 FO K3PN0 NS 854161209
>
> SUSAP KFODK3FL06 L 021ATLOJK3PC0E S CF IFODK3 2430003406300028PI + 01820 -324 0 NS 854171310
>
> SUSAP KFODK3FL06 L 030RW06 K3PG0GY M CF IFODK3 2430001306300021PI 01134 -324 0 NS 854181209
>
> SUSAP KFODK3FL06 L 040 0 M CA 0630 + 02800 0 NS 854191209
>
> SUSAP KFODK3FL06 L 050FOD K3D 0VY L DF + 02800 0 NS 854201209
>
> SUSAP KFODK3FL06 L 060FOD K3D 0VE R HM 1204T010 + 02800 0 NS 854211209

-   GPS approach on RWY29 at KFOT

> SUSAP KFOTK2FP29 ADINSE 010DINSEK2EA0E A IF 18000 P PS 857251211
>
> SUSAP KFOTK2FP29 ADINSE 020JEWPEK2PC0E TF + 06000 P PS 857261310
>
> SUSAP KFOTK2FP29 ADINSE 030SPHERK2PC0EE TF + 04700 P PS 857271310
>
> SUSAP KFOTK2FP29 AKNEES 010KNEESK2EA0E IF 18000 P PS 857281211
>
> SUSAP KFOTK2FP29 AKNEES 020YAGERK2EA0E A TF + 06800 P PS 857291211
>
> SUSAP KFOTK2FP29 AKNEES 030SPHERK2PC0EE TF + 04700 P PS 857301310
>
> SUSAP KFOTK2FP29 APLYAT 010PLYATK2EA0E A IF 18000 P PS 857311211
>
> SUSAP KFOTK2FP29 APLYAT 020JEVGYK2PC0E TF + 06000 P PS 857321310
>
> SUSAP KFOTK2FP29 APLYAT 030SPHERK2PC0EE TF + 04700 P PS 857331310
>
> SUSAP KFOTK2FP29 P 010SPHERK2PC0E I IF + 04700 18000 P PS 857341310
>
> SUSAP KFOTK2FP29 P 011JEVSYK2PC0E S TF + 03700 P PS 857351310
>
> SUSAP KFOTK2FP29 P 020ELLYSK2PC0E F TF + 02700 IZPUH K2PCP PS 857361310
>
> SUSAP KFOTK2FP29 P 021<span style="color:blue">**SP29**</span> K2PC0E A TF + 01840 -356 P PS 857371211
>
> SUSAP KFOTK2FP29 P 030IZPUHK2PC0EY M TF 00617 -356 P PS 857381310
>
> SUSAP KFOTK2FP29 P 040 0 M CA 2757 + 01500 P PS 857391211
>
> SUSAP KFOTK2FP29 P 050FOT K2D 0VY R DF + 03000 P PS 857401211
>
> SUSAP KFOTK2FP29 P 060FOT K2D 0VE R HM 1610T010 + 03000 P PS 857411510

-   In the European AIS Database (EAD). A query on the EAD helped
    identify the following:

    -   40 occurrences of designated points with a 1-letter designator

    -   190 occurrences of designated points with a 2-letters designator

    -   356 occurrences of designated points with a 3-letters designator

    -   3046 occurrences of designated points with a 4-letters
        designator

 Most of the designated points with 2, 3 or 4-letter designators
 actually correspond to designated points formed after the position of
 a navaid or an aerodrome. However, some occurrences cannot be related
 to these cases. The following table quotes a few random examples.

| **Designator**        | **Lat**      | **Long**      | **Type** |
|-----------------------|--------------|---------------|----------|
| *1-letter designator* |              |               |          |
| N                     | 503241N      | 0042718E      | ADHP     |
| E                     | 502941N      | 0044206E      | ADHP     |
| A                     | 475826.0000N | 0161445.0000E | OTHER    |
| B                     | 475202.0000N | 0161514.0000E | OTHER    |
| B                     | 475734.0000N | 0161614.0000E | OTHER    |
| *2-letter designator* |              |               |          |
| S1                    | 460217N      | 0142702E      | OTHER    |
| S2                    | 460552N      | 0142748E      | OTHER    |
| S3                    | 460845N      | 0142452E      | OTHER    |
| W1                    | 461846N      | 0141449E      | OTHER    |
| W2                    | 461744N      | 0142049E      | OTHER    |
| A1                    | 453243N      | 0181709E      | OTHER    |
| A2                    | 423842N      | 0175651E      | ADHP     |
| A3                    | 434049N      | 0155502E      | ADHP     |
| *3-letter designator* |              |               |          |
| PJ1                   | 544005N      | 0253057E      | OTHER    |
| PJ2                   | 554243N      | 0211434E      | OTHER    |
| PJ3                   | 561350N      | 0221534E      | OTHER    |
| TP1                   | 481018.5959N | 0113540.1135E | ADHP     |
| TP2                   | 481314.6479N | 0113003.5398E | ADHP     |
| 13A                   | 512900.0000N | 0185100.0000E | OTHER    |
| 17A                   | 520800.0000N | 0174500.0000E | OTHER    |
| 20A                   | 514400.0000N | 0160800.0000E | OTHER    |
| 21A                   | 521300.0000N | 0162800.0000E | OTHER    |
| 27A                   | 523500.0000N | 0160500.0000E | OTHER    |
| ME1                   | 462338N      | 0155433E      | OTHER    |
| ME2                   | 463440N      | 0155009E      | OTHER    |
| DX1                   | 481323.7979N | 0122604.5443E | ADHP     |
| DX1                   | 505951.6149N | 0141519.3372E | ADHP     |
| DX2                   | 505625.7259N | 0141418.8105E | ADHP     |
| *4-letter designator* |              |               |          |
| SJ91                  | 000224S      | 1044205E      | OTHER    |
| FF36                  | 375713.7253S | 1751802.2481E | ADHP     |
| SM01                  | 464002.55N   | 0170535.52E   | ADHP     |
| SM02                  | 464750.28N   | 0165926.87E   | ADHP     |
| LDD5                  | 514138.7284N | 0191615.9435E | ADHP     |
| PG23                  | 545132N      | 0230742E      | OTHER    |
| IF09                  | 431021.1N    | 0252819.1E    | ADHP     |
| MAPT                  | 492818.3679N | 0083229.2734E | ADHP     |
| SDF2                  | 495218.7277N | 0112216.9347E | ADHP     |
| FF16                  | 513503.7055N | 1124320.1363W | ADHP     |
| ECHO                  | 504818N      | 0051950E      | ADHP     |
| ECHO                  | 482129.75N   | 0703422.93W   | OTHER    |

#### References to Navaid

`FIXM TYPE` [fb:NavaidType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_NavaidType.html#Link16)

<img src="./media/NavaidType.png">

##### OPTION 1 - Minimum reference

As a minimum, the coded designator of a navaid shall be provided, as
published in the AIPs.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:navaid>
   <fb:designator>BOR</fb:designator>
</fb:navaid>
```

##### OPTION 2 - Unambiguous reference

*(OPTION 2 = OPTION 1 + supplementary position & navaidServicetype
information)*

The coded designator of a navaid is not always sufficient for
unambiguously referring to that element. The en-route navaids (VOR, DME,
NDB) designator is supposed to be unique (according to ICAO Annex 11)
within 600 NM. This means that these designators are not unique
world-wide. For airport navaids, there is no limitation.

FIXM adds two optional properties 'position' and 'navaidServiceType'
which may be used as a complement to the 'designator' information in
order to remove any ambiguity on the designator.

Important note: the combination of fields \[designator + position +
navaid service type\] shall not be interpreted as a natural key uniquely
identifying the navaid, in so far as producing and consuming
systems/services may use different aeronautical information sources with
different degree of precisions for lat/long, leading to small variations
of the position information. The supplementary fields shall be used for
disambiguation purposes only (i.e. plausibility checks based on position
and navaid service type) in case of duplicate/triplicate/…

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:navaid>
   <fb:designator>BOR</fb:designator>
   <fb:navaidServiceType>VOR_DME</fb:navaidServiceType>
   <fb:position srsName="urn:ogc:def:crs:EPSG::4326">
      <fb:pos>52.36833333333333 -32.375</fb:pos>
   </fb:position>
</fb:navaid>
```

##### OPTION 3 - Minimum reference with supplementary AIXM pointer

*(OPTION 3 = OPTION 1 + supplementary hypertext reference)*

Option 3 corresponds to Option 1 with an additional hypertext reference
as described in chapter Generic hypertext references.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:navaid href="urn:uuid:08a1bbd5-ea70-4fe3-836a-ea9686349495">
   <fb:designator>BOR</fb:designator>
</fb:navaid>
```

##### OPTION 4 - Complete reference

*(OPTION 4 = OPTION 2 + OPTION 3)*

Option 4 corresponds to the combination of Option 2 and Option 3. See
explanations above.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:navaid href="urn:uuid:08a1bbd5-ea70-4fe3-836a-ea9686349495">
   <fb:designator>BOR</fb:designator>
   <fb:navaidServiceType>VOR_DME</fb:navaidServiceType>
   <fb:position srsName="urn:ogc:def:crs:EPSG::4326">
      <fb:pos>52.36833333333333 -32.375</fb:pos>
   </fb:position>
</fb:navaid>
```



#### References to Aerodromes

`FIXM TYPE` [fb:AerodromeReferenceType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_AerodromeReferenceType.html#LinkC)

<img src="./media/AerodromeReferenceType.png">


##### OPTION 1 - Minimum reference

The minimum aerodrome reference shall consist of the aerodrome location
indicator, if provided by ICAO Doc 7910 \[11\]. If the aerodrome has no
ICAO Doc 7910 location indicator, the minimum aerodrome reference shall
consist of the name of the aerodrome and its geographical location,
namely the aerodrome reference point.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

CASE 1 – The Aerodrome has an ICAO Doc 7910 location indicator

```xml
<fx:destinationAerodrome>
   <fb:locationIndicator>EADD</fb:locationIndicator>
</fx:destinationAerodrome>
```

CASE 2 – The Aerodrome has no ICAO Doc. 7910 location indicator

```xml
<fx:destinationAerodrome>
   <fb:name>DONLON</fb:name>
   <fb:referencePoint srsName="urn:ogc:def:crs:EPSG::4326">
      <fb:pos>52.3716666666667 -31.9494444444444</fb:pos>
   </fb:referencePoint>
</fx:destinationAerodrome>
```

##### OPTION 2 - Minimum reference with supplementary AIXM pointer

*(OPTION 2 = OPTION 1 + supplementary hypertext reference)*

Option 2 corresponds to Option 1 with an additional hypertext reference
as described in chapter Generic hypertext references.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

CASE 1 – The Aerodrome has an ICAO Doc 7910 location indicator

```xml
<fx:destinationAerodrome href="urn:uuid:1b54b2d6-a5ff-4e57-94c2-f4047a381c64">
   <fb:locationIndicator>EADD</fb:locationIndicator>
</fx:destinationAerodrome>
```

CASE 2 – The Aerodrome has no ICAO Doc. 7910 location indicator

```xml
<fx:destinationAerodrome href="urn:uuid:1b54b2d6-a5ff-4e57-94c2-f4047a381c64">
   <fb:name>DONLON</fb:name>
   <fb:referencePoint srsName="urn:ogc:def:crs:EPSG::4326">
      <fb:pos>52.3716666666667 -31.9494444444444</fb:pos>
   </fb:referencePoint>
</fx:destinationAerodrome>
```


Important note: FIXM enables the encoding of richer aerodrome
“reference” structures, such as

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:destinationAerodrome href="urn:uuid:1b54b2d6-a5ff-4e57-94c2-f4047a381c64">
   <fb:iataDesignator>DLN</fb:iataDesignator>
   <fb:locationIndicator>EADD</fb:locationIndicator>
   <fb:name>DONLON</fb:name>
   <fb:referencePoint srsName="urn:ogc:def:crs:EPSG::4326">
      <fb:pos>52.3716666666667 -31.9494444444444</fb:pos>
   </fb:referencePoint>
</fx:destinationAerodrome>
```

The provision of the name and reference point in addition to the
location indicator is technically possible but does not serve the
purpose of identifying the aerodrome. This implementation practice only
aims to provide consumers with richer information about the aerodrome
being referred to. Whether or not to use this supplementary information
is at the discretion of the consuming system / service.

#### References to Runway Directions

`FIXM TYPE` [fb:RunwayDirectionDesignatorType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_RunwayDirectionDesignatorType.html#Link1C)

<img src="./media/RunwayDirectionDesignatorType.png">

##### OPTION 1 - Minimum reference

The minimum Runway Direction reference shall consist of the Runway
Direction designator.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:runwayDirection>09L</fx:runwayDirection>
```

##### OPTION 2 - Minimum reference with supplementary AIXM pointer

*(OPTION 2 = OPTION 1 + supplementary hypertext reference)*

Option 2 corresponds to Option 1 with an additional hypertext reference
as described in chapter Generic hypertext references.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:runwayDirection href="urn:uuid:c8455a6b-9319-4bb7-b797-08e644342d64">09L</fx:runwayDirection>
```


#### References to Enroute ATS routes

`FIXM TYPE` [fb:RouteDesignatorType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_RouteDesignatorType.html#Link1A)

<img src="./media/RouteDesignatorType.png">

##### OPTION 1 - Minimum reference

The minimum Enroute ATS Route reference shall consist of the Enroute ATS
Route designator as published in the AIP. Enroute ATS Route designators
are not unique. However, the systematic pairing of an ATS route
designator with a route point (i.e. a significant point belonging to
that ATS route) in FIXM is considered sufficient for enabling
unambiguous identification of the ATS Route being referred to.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:routeDesignator>UA4</fx:routeDesignator>
```

##### OPTION 2 - Minimum reference with supplementary AIXM pointer

*(OPTION 2 = OPTION 1 + supplementary hypertext reference)*

Option 2 corresponds to Option 1 with an additional hypertext reference
as described in chapter Generic hypertext references.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:routeDesignator href="urn:uuid:a14a8751-5428-46bc-a2d1-32ef84d37b5c">UA4</fx:routeDesignator>
```

#### References to SIDs and STARs

`FIXM TYPE` [fb:SidStarReferenceType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_SidStarReferenceType.html#Link1E)

<img src="./media/SidStarReferenceType.png">

##### OPTION 1 - Minimum reference

The minimum SID or STAR reference shall consist of the SID or STAR
designator as published in the AIP.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:standardInstrumentDeparture>
   <fb:designator>AMOLO5B</fb:designator>
</fx:standardInstrumentDeparture>

```

##### OPTION 2 - Minimum reference with supplementary AIXM pointer

*(OPTION 2 = OPTION 1 + supplementary hypertext reference)*

Option 2 corresponds to Option 1 with an additional hypertext reference
as described in chapter Generic hypertext references.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:standardInstrumentDeparture href="urn:uuid:...">
   <fb:designator>AMOLO5B</fb:designator>
</fx:standardInstrumentDeparture>
```


FIXM also supports the supplementary provision of the abbreviated
designator of the SID or the STAR which is commonly used in FMS
databases and in some ground automation systems. The ‘abbreviated
designator’, if provided, should be the designator obtained after
applying the rules for shortening names specified by the ARINC 424
specification, chapter 7.4. Example:

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:standardInstrumentDeparture>
   <fb:abbreviatedDesignator>AMOL5B</fb:abbreviatedDesignator>
   <fb:designator>AMOLO5B</fb:designator>
</fx:standardInstrumentDeparture>
```


#### References to Airspace

`FIXM TYPE` [fb:AirspaceDesignatorType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_AirspaceDesignatorType.html#LinkE)

<img src="./media/AirspaceDesignatorType.png">


##### OPTION 1 - Minimum reference

The minimum airspace reference shall consist of the airspace location
indicator, if provided by ICAO Doc 7910 \[11\]. If the airspace has no
ICAO Doc 7910 location indicator, the minimum airspace reference shall
consist of the coded designator of the airspace as published in the AIP.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

CASE 1 – The Airspace has an ICAO Doc 7910 location indicator

```xml
   <fx:region>KZLC</fx:region>
```

CASE 2 – The Airspace has no ICAO Doc. 7910 location indicator

```xml
<fx:region>AMSWELL</fx:region>
```

##### OPTION 2 - Minimum reference with supplementary AIXM pointer

*(OPTION 2 = OPTION 1 + supplementary hypertext reference)*

Option 2 corresponds to Option 1 with an additional hypertext reference
as described in chapter Generic hypertext references.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

CASE 1 – The Airspace has an ICAO Doc 7910 location indicator

```xml
<fx:region href="urn:uuid:...">KZLC</fx:region>
```

CASE 2 – The Airspace has no ICAO Doc. 7910 location indicator

```xml
<fx:region href="urn:uuid:...">AMSWELL</fx:region>
```


#### References to (ATC) Units

`FIXM TYPE` [fb:AtcUnitReferenceType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_AtcUnitReferenceType.html#Link10)

<img src="./media/AtcUnitReferenceType.png">

##### OPTION 1 - Minimum reference

The minimum ATC unit reference shall consist of the location indicator
of the unit, if provided by ICAO Doc 7910 \[11\]. If the unit has no
ICAO Doc 7910 location indicator, the minimum airspace reference shall
consist of the name of the unit or any alternate name, as published in
the AIP.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

CASE 1 – The Unit has an ICAO Doc 7910 location indicator

```xml
<fx:originator>
   <fb:locationIndicator>EBBU</fb:locationIndicator>
</fx:originator>
```

CASE 2 – The Unit has no ICAO Doc. 7910 location indicator

```xml
<fx:originator>
   <fb:atcUnitNameOrAlternate>MILITARY DONLON TWR</fb:atcUnitNameOrAlternate>
</fx:originator>
```

##### OPTION 2 - Minimum reference with supplementary AIXM pointer

*(OPTION 2 = OPTION 1 + supplementary hypertext reference)*

Option 2 corresponds to Option 1 with an additional hypertext reference
as described in chapter Generic hypertext references.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

CASE 1 – The Unit has an ICAO Doc 7910 location indicator

```xml
<fx:originator href="urn:uuid:...">
   <fb:locationIndicator>EBBU</fb:locationIndicator>
</fx:originator>
```

CASE 2 – The Unit has no ICAO Doc. 7910 location indicator

```xml
<fx:originator href="urn:uuid:...">
   <fb:atcUnitNameOrAlternate>MILITARY DONLON TWR</fb:atcUnitNameOrAlternate>
</fx:originator>
```



### Relative points

`FIXM TYPE` [fb:RelativePointType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_RelativePointType.html#Link18)

<img src="./media/RelativePointType.png">

A relative point is a bearing and distance from a reference navaid.
Encoding a relative point in FIXM requires the ‘bearing’, ‘distance’ and
‘referencePoint’ properties to be provided. All of these properties
shall be provided.

FIXM enables a relative point to be supplemented with an optional
‘position’ value for storing the actual position of the relative point
if already known. The exchange of this information may prove useful in
order to save consuming systems / services from (re)computing the
position of the relative point. Whether or not to use this supplementary
information is at the discretion of the consuming system / service.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

Without position information

```xml
<fb:relativePoint>
   <fb:bearing uom="DEG" zeroBearingType="MAGNETIC_NORTH">82.0</fb:bearing>
   <fb:distance uom="NM">2.0</fb:distance>
   <fb:referencePoint>
      <fb:designator>BOR</fb:designator> (*)
   </fb:referencePoint>
</fb:relativePoint>
```

With position information

```xml
<fb:relativePoint>
   <fb:bearing uom="DEG" zeroBearingType="TRUE_NORTH">180</fb:bearing>
   <fb:distance uom="NM">60.0</fb:distance>
   <fb:position srsName="urn:ogc:def:crs:EPSG::4326">
      <fb:pos>51.36833333333333 -32.375</fb:pos>
   </fb:position>
   <fb:referencePoint>
      <fb:designator>BOR</fb:designator> (*)
   </fb:referencePoint>
</fb:relativePoint>
```

(*) See chapter References to Navaid above. All four options can be used for encoding this reference. OPTION 1 is used in this example.</td>



### Vertical distances

`FIXM TYPE` [fb:VerticalDistanceType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_VerticalDistanceType.html#Link86)
`FIXM TYPE` [fb:AltitudeType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_AltitudeType.html#Link6B)
`FIXM TYPE` [fb:FlightLevelType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_FlightLevelType.html#Link73)
`FIXM TYPE` [fb:HeightType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_HeightType.html#Link79)


The term vertical distance collectively refers to altitudes, elevations
and heights, as defined by ICAO

> **Altitude**. The vertical distance of a level, a point or an object considered as a point, measured from mean sea level (MSL).

> **Elevation**. The vertical distance of a point or a level, on or affixed to the surface of the earth, measured from mean sea level.

> **Height**. The vertical distance of a level, a point or an object considered as a point, measured from a specified datum.

> **Ellipsoid height**. The height related to the reference ellipsoid, measured along the ellipsoidal outer normal through the point in question.


<img src="./media/vertical_distances.png" />

Figure: Differences between Elevation, Altitude, Height and Ellipsoid height


FIXM supports the representation of altitudes expressed in feet or
meters (FIXM construct ‘Altitude’), of altitudes expressed as flight
level number or standard metric level (FIXM construct ‘FlightLevel’) and
of ellipsoid heights & heights SFC expressed in feet or meter (FIXM
construct ‘Height’ used in conjunction with a VerticalReference).

<img src="./media/VerticalDistanceType.png" />

These vertical distances are specialisations of the generic class
Measure which serves as the parent class for all measure types including
speeds, angles, pressures, temperatures etc. Therefore, altitudes,
flight levels and heights are always encoded as double values, although
integer values are expected. “Double Integer” conversion can be handled
differently depending on the technical context. This may lead to e.g.
flight level value 100 being expressed as 100.00…0001 and the flight
level value 101 being expressed as 100.99…99999. It is acknowledged that
the current FIXM design may create value persistence problem across
applications, in particular if rounding or truncation are applied
further down. FIXM implementers are therefore invited to verify the
persistence of vertical distances values across their software.


The following examples show valid FIXM encoding of altitudes and flight
levels expressed as integer.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fb:altitude uom="FT">10000</fb:altitude>
```

```xml
<fb:altitude uom="M">3500</fb:altitude>
```

```xml
<fb:flightLevel uom="FL">290</fb:flightLevel>
```

```xml
<fb:flightLevel uom="SM">1130</fb:flightLevel>
```

`EXAMPLE` <img src="./media/nok.png" style="width:0.25in;height:0.25in" />

The following example shows the encoding of a flight level expressed as
a double. This encoding is technically permitted by FIXM but is NOT
recommended.

```xml
<fb:flightLevel uom="FL">290.0</fb:flightLevel>
```

### Sequence numbers

The FIXM Logical Model specifies several ordered repeating sequences.
The FIXM XML schemas add an optional sequence number attribute to the
repeating elements in order to ensure that the order of a sequence is
always preserved, even after XSLT manipulation.

The sequence number should be a sequentially increasing integer with a
value beginning at zero. These sequence numbers are only meant for
ordering, not identification, purposes. As such, the set of sequence
numbers taken as a whole should always be contiguous. If an element were
removed from a sequence, the numbering in subsequent representations
should be reset to reflect this, not maintained so that a gap is formed.

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:filed>
   <!-- First element of the sequence -->
   <fx:element seqNum="0">
      [...]
   </fx:element>
   <!-- Next element of the sequence -->
   <fx:element seqNum="1">
      [...]
   </fx:element>
   [...]
</fx:filed>
```


### Contact Information

`FIXM TYPE` [fb:ContactInformationType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_ContactInformationType.html#Link1)

<img src="media/ContactInformationType.png"/>

#### Postal Address

> TODO

#### Telephone Contact

> TODO

#### Online Contact

`FIXM TYPE` [fb:OnlineContactType](https://www.fixm.aero/releases/FIXM-4.2.0/doc/schema_documentation/Fixm_ContactInformationType.html#Link1)

In FIXM, the online contact information can include an email address
and/or a network address.

A network address is always formed of two pieces of information: the
**linkage** and the **network** information.

-   The former captures the expression of the network address. This is
    supported by property OnlineContact.linkage.

-   The latter captures the network on which the address is valid. This
    is supported by property OnlineContact.network.

Property OnlineContact.network provides a choice between predefined
network types and free text. Network information should be preferably
encoded using the property NetworkChoice.**type** populated with the
applicable enumerated value from enumeration TelecomNetworkType. If none
of the enumerated values is suitable, the property NetworkChoice.other
shall be used. The ATM Information Reference Model provides additional
telecom network types that should be used for populating
NetworkChoice.other, as appropriate. These additional AIRM values are
available at the following link:

<http://airm.aero/viewer/1.0.0/logical-model.html#CodeTelecomNetworkType>

The type of network affects the format of the linkage information.

-   When the network is INTERNET, the linkage should be a resolvable URL

-   When the network is AFTN, the linkage should be a valid AFTN address

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

The following example illustrates contact information formed of an email
address and an Internet address expressed as a resolvable URL.

```xml
<fb:onlineContact>
   <fb:email>fixm.secretariat@eurocontrol.int</fb:email>
   <fb:linkage>https://www.fixm.aero/content/contact.pl</fb:linkage>
   <fb:network>
      <fb:type>INTERNET</fb:type>
   </fb:network>
</fb:onlineContact>
```

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

The following example illustrates contact information formed of an AFTN
address. The example features the EUROCONTROL NM ‘AFTN address’ for
Flight Plan Message Submission to IFPS (FP1 - Brussels (Haren)).

```xml
<fb:onlineContact>
  <fb:linkage>EUCHZMFP</fb:linkage>
  <fb:network>
     <fb:type>AFTN</fb:type>
  </fb:network>
</fb:onlineContact>
```

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

The following example illustrates contact information formed of a SITA
address. ‘SITA’ is not a value captured in FIXM enumeration
TelecomNetworkType but is part of the reference ATM vocabulary provided
by the AIRM ([AIRM codelist
CodeTelecomNetworkType](http://airm.aero/viewer/1.0.0/logical-model.html#CodeTelecomNetworkType)).
The example features the EUROCONTROL NM ‘SITA address’ for Flight Plan
Message Submission to IFPS (FP1 - Brussels (Haren)).

```xml
<fb:onlineContact>
   <fb:linkage>BRUEP7X</fb:linkage>
   <fb:network>
      <fb:other>SITA</fb:other>
   </fb:network>
</fb:onlineContact>
```

### Version numbers

*This is a placeholder for further guidance on how to encode version
numbers. This chapter will be populated in a future version of the
document.*

### Aircraft Types

*This is a placeholder for further guidance on how to encode aircraft
types. This chapter will be populated in a future version of the
document.*

### Rules for encoding a 4D Trajectory 

*This is a placeholder for further guidance on how to encode a 4D
trajectory. This chapter will be populated in a future version of the
document.*

### Rules for encoding Constraints 

*This is a placeholder for further guidance on how to encode
constraints. This chapter will be populated in a future version of the
document.*

### General rules for data correctness

*Important note: in the present version of the document, limited effort
could be spent on the documentation of FIXM business rules addressing
data correctness. The list below provides an initial set of rules that
were identified during the writing of this document or that are already
captured in the FIXM model as model element notes. Future versions of
the document will enrich this table based on implementers’ feedback, and
may also revisit the overall formulation and description method for
these rules, in particular in the light of the [AIXM experience with
regards to business rules](http://aixm.aero/page/business-rules).*

|FIXM model element|Business rule description|
|:-|:-----|
|GeograhicalPosition|When encoding geographical positions, special values INF, -INF and NaN are not allowed.|
|ContactInformation,<p>PersonOrOrganization|If the usage of ContactInformation is associated with a person, the ContactInformation.name field should not be used and the PersonOrOrganization.name should be used instead.|
|PostalAddress|The countryName shall always be populated with the full name, not an ISO 3166 abbreviation.|
|PostalAddress|CountryCode shall always be populated using an ISO 3166 abbreviation.|
|OnlineContact|If OnlineContact.network.type=INTERNET, the OnlineContact.linkage shall be expressed as a valid URL.|
|OnlineContact|If OnlineContact.network.type=AFTN, the OnlineContact.linkage shall be expressed as a valid AFTN address.|
|AerodromeReference|The locationIndicator shall be a valid code provided by ICAO Doc 7910.|
|VerticalRange|The lowerBound shall always be lower than the upperBound.|
|TrueAirspeedRange|The lowerSpeed shall always be lower than the upperSpeed.|
|TimeRange|The earliest time shall always be before the latest time.|
|Aircraft|The property formationCount, if provided, shall be equal to or greater than 2.|
|Aircraft, AircraftType|The sum of all the AircraftType.numberOfAircraft properties, if provided, shall match Aircraft.formationCount.|
|OnlineContact|The OnlineContact.network shall be always populated when providing an OnlineContact.linkage.|

### Rules for absent data

FIXM supports the representation of fields that are explicitly absent or
that are deleted. It does so by leveraging the XSD specification for
Elements which includes the *nillable* attribute. This “nillable”
attribute specifies whether an explicit null value can be assigned to
the element. When nillable is set to “true” in the element definition,
this in turn enables an instance of the element to have the built-in nil
attribute with a value set to “true”. Example:

```xml
<xs:complexType name="FlightType"> 
   [...]  
   <xs:element name="dangerousGoods" type="fx:DangerousGoodsType" nillable="true" [...]> 
   [...]  
</xs:complexType>
```

<img src="./media/Double_arrow_symbol_-_blue.png" style="width:0.50in;height:0.70in" />

```xml
<fx:Flight> 
   <fx:dangerousGoods xsi:nil="true"/> 
</fx:Flight>
```

FIXM does not support the exchange of a “nil reason” to explain why an
element is nil. The interpretation of a nil element therefore depends on
the context of the information exchange:

-   A nil element included in an FF-ICE Flight Plan Update message will
    indicate that this flight plan data item is to be deleted. This
    interpretation is dictated by the FF-ICE Implementation Guidance
    Manual which states the following:

> *7.4.3.6 A Flight Plan Update is only required to contain those items
> that have changed (in addition to the mandatory items specified for an
> Update message), i.e. it is not necessary to resend complete flight
> data. Data items that were included in the previous version of the
> flight plan and have not been included in the Flight Plan Update will
> remain unchanged. This means that a mechanism is required to identify
> when a flight plan data item is to be deleted.”*

-   A nil element included in an FF-ICE Flight Data Response Message
    will indicate that the data item is explicitly declared as not
    available to the flight data requestor.

Future FIXM versions may support the exchange of an additional “nil
reason” attribute, if the need for it is identified by the FIXM
Community.

*Note: the support for nillable elements has implied a significant
design change in FIXM Core 4.2.0. The previous FIXM Core versions relied
extensively on XSD attributes, which are not nillable. These XSD
attributes were converted to XSD elements in FIXM Core 4.2.0 so that the
built-in XSD attribute nillable could be leveraged.*

##### Declaring null Measures and Geographical Position

The FIXM Measures types enforce the provision of the “uom” attribute
together with the numeric value of the measure. Likewise, the FIXM
Geographical Position type enforces the provision of the srsName
attribute “urn:ogc:def:crs:EPSG::4326” together with the position. This
design guarantees that the required unit of measure and coordinate
reference system are always provided in order to enable the correct
interpretation of measures and positions. However, it requires a special
workaround when null values are to be exchanged.

The provision of a null value for a measure or a position still requires
the mandatory attribute “uom” or “srsName” to be provided, even if
meaningless. For instance, the following XML data would NOT validate
against the FIXM Core schema, because the uom for a Mass is missing.

`EXAMPLE` <img src="./media/nok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:desired>
   <fx:takeoffMass xsi:nil="true/">
</fx:desired>
```

Therefore, the following rules apply when declaring null Measures or
Geographical Position.

When a measure is to be declared null,

-   Information provider side: Provide a fake uom in order to ensure
    proper schema validation

-   Information consumer side: Ignore the fake uom provided together
    with the null measure

When a geographical position is to be declared null:

-   Information provider side: Provide the srsName
    “urn:ogc:def:crs:EPSG::4326” in order to ensure proper schema
    validation

-   Information consumer side: Ignore the provided srsName provided
    together with the null position.

Example of valid null measure declaration with a fake uom to be ignored:

`EXAMPLE` <img src="./media/ok.png" style="width:0.25in;height:0.25in" />

```xml
<fx:desired>
   <fx:takeoffMass xsi:nil="true" uom="KG"/>
</fx:desired>
```



## Other Topics

### The use of other exchange models

*An information exchange model is designed to enable the sharing of
information in a digital format within a specific domain*[6] (e.g. AIXM
for the aeronautical information domain or FIXM for the flight
information domain). However, some ATM operations may require ATM
information to be treated and exchanged in a more interrelated way. For
example, a single traffic flow management message may include both
airspace geometries (aeronautical information domain) as well as
information about the flights passing through them (flight information
domain).

Satisfying these cross-cutting information needs can be done in
different manners. Combining data from existing information exchange
models (e.g. AIXM and FIXM) is one approach. This document briefly
touches on two possible options for doing so in an attempt to aid FIXM
users wishing to create multi-model data exchanges.

#### Correlation references

The first option to consider is breaking down a multi-model data
transmission into separate messages for each involved exchange model.
These messages are supplemented with correlation references to all other
component messages of the overall multi-model transmission. Supplying a
list of unique message identifiers for each entry in the message group
should be sufficient. This lets an end user know that such a message
should not be processed alone and which other messages are intended to
accompany it. Message identifiers and references can likely be handled
by the data exchange’s messaging layer without the need to modify the
exchange models themselves to accommodate this approach. However, the
end user must perform the correlation work themselves. This would
include creating procedures for how to handle a situation where only a
subset of a message group is received.

#### Correlation models

The second option to consider is creating a new model that provides
direct references within itself to the required exchange models – thus
allowing multiple models to be used together in a single data
transmission. This approach frees end users from the burden of
correlating messages (and the associated dangers of partial data loss).
However, this approach cannot be as easily leveraged by systems already
capable of handling the underlying exchange models used. The new model
must first be created and incorporated into the systems that transmit
and receive this data.

