`FF-ICE GUIDANCE`

This page provides **informative** examples of
service descriptions to realize the FF-ICE/R1 Planning Service.

***

`EXAMPLE`

**Technology Selection**

For this example, two sets of technologies have been selected that accommodate the need to exchange
the FF-ICE/R1 messages:

-   **SOAP Web Service technologies** allow to exchange request and
    reply messages where the initiative is taken by the service
    consumer.[10]

-   **AMQP** supports the exchange of notification messages where the
    initiative is taken by the service provider.

Appendix XXX Section YYY provides detailed rational for the technology
selection.

**Description**

|**Service Name**|
|:---------------|
|Planning Service|

|**Abstract**|
|:-----------|
|The Planning Service enables a CDM process between the eAU and the eASP(s) concerning the intended operation of a flight. It is described in \[10\], Page II-5-21 Section 5.1.|

|**Provision**           |      |
|:---                    |:-----|
|**Provider**            | Example Air Services (XAS)|
|**Provider Description**| XAS is a fictitious eASP used for illustration purposes.|
|**Provider Type**       | The Planning Service is an optional service expected or recommended to be provided by an eASP whose airspace is complex and/or regularly constrained. |

|**Categorisation**                      |   |
|:--                                     |:---------------|
|**Service Type**                        | Swim compliant |
|**Life Cycle Stage**                    | Operational  |
|**Business Activity Type**              | Demand and capacity balancing |
|**Information Category**                | Cooperative network information exchange |
|**Intended Consumer**                   | The Planning Service is consumed by eAU(s). |
|**Application Message Exchange Pattern**| Request Reply |

|**Operational Need**|
|:-------------------|
|Assist the operator in determining the optimal route/trajectory for a flight by identifying the operational environment and ATM constraints applicable to the flight as proposed. |                                                                                                                                                       |
|Enable ATM service providers to obtain an earlier, more detailed and more accurate assessment of the anticipated traffic demand.                                                  |                                                                                                                                                       |

|**Functionality**  |
|:----------------- |
|The ability to submit a preliminary flight plan and associated messages (Update, Cancel) and to provide the appropriate response messages (Submission Response, Planning Status)  |

|**Service Interface**                     |                                  |
|:---------------------------------------- |----------------------------------|
|**Name**                                  | Planning Provider eASP Interface |
|**Description**                           | TBD                              |
|**Interface provision side**              | Provider side interface          |
|**TI primitive message exchange pattern** | Synchronous request response     |
|**Service interface binding**             | SWIM\_TI\_YP\_1\_0\_WS\_SOAP     |
|**Network interface binding**             | TI\_YP\_1\_0.IPV4\_UNICAST       |

|**Operation**          |                                  |
|:----------------------|:---------------------------------|
|**name**               | submitPreliminaryFlightPlan |
|**description**        | Request the submission of a new Preliminary Flight Plan |
|**Idempotency**        | Idempotent |
|**Synchronicity**      | Synchronous |
|**TI Protocol method** | HTTP POST |
    
|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | preliminaryFlightPlanSubmissionRequest |
|**Description**        | Request of the submission of a new Preliminary Flight Plan.<br><br> `MESSAGE TEMPLATE` [FficePFP_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/preliminaryflightplan/fficemessage/FficePFP_FficeMessage.xsd)|
|**Direction**          | In |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |

|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | preliminaryFlightPlanSubmissionReply |
|**Description**        | Reply returned in response to preliminaryFlightPlanSubmissionRequest.<br><br> `MESSAGE TEMPLATE` [FficeSR_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/submissionresponse/fficemessage/FficeSR_FficeMessage.xsd)|
|**Direction**          | Out |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |

|**Operation**          |                                  |
|:----------------------|:---------------------------------|
|**name**               | updatePreliminaryFlightPlan |
|**description**        | Request the update of a Preliminary Flight Plan |
|**Idempotency**        | Non idempotent |
|**Synchronicity**      | Synchronous |
|**TI Protocol method** | HTTP POST |

|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | preliminaryFlightPlanUpdateRequest |
|**Description**        | Request the update of a Preliminary Flight Plan<br><br> `MESSAGE TEMPLATE` [FficeFPU_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/submissionresponse/fficemessage/FficeFPU_FficeMessage.xsd)|
|**Direction**          | In |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |

|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | preliminaryFlightPlanUpdateReply |
|**Description**        | Reply returned in response to preliminaryFlightPlanUpdateRequest <br><br> `MESSAGE TEMPLATE` [FficeSR_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/submissionresponse/fficemessage/FficeSR_FficeMessage.xsd)|
|**Direction**          | Out |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |

|**Operation**          |                                  |
|:----------------------|:---------------------------------|
|**name**               | cancelPreliminaryFlightPlan |
|**description**        | Request the cancellation of a Preliminary Flight Plan |
|**Idempotency**        | Non idempotent |
|**Synchronicity**      | Synchronous |
|**TI Protocol method** | HTTP POST |

|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | preliminaryFlightPlanCancellationRequest |
|**Description**        | Request the cancellation of a Preliminary Flight Plan<br><br> `MESSAGE TEMPLATE` [FficeFC_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/submissionresponse/fficemessage/FficeFC_FficeMessage.xsd)|
|**Direction**          | In |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |

|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | preliminaryFlightPlanCancellationReply |
|**Description**        | Reply returned in response to preliminaryFlightPlanCancellationRequest<br><br> `MESSAGE TEMPLATE` [FficeSR_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/submissionresponse/fficemessage/FficeSR_FficeMessage.xsd)|
|**Direction**          | Out |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |

Appendix XXX Section YYY provides detailed information regarding… TBD

***

`EXAMPLE`

> TODO