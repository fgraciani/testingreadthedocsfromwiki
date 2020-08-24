`FF-ICE GUIDANCE`

This page provides **informative** examples of
service descriptions to realize the FF-ICE/R1 Filing Service.

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
|Filing Service |

|**Abstract**|
|:-----------|
|The Filing Service enables the submission of filed flight plans (eFPL) in order to obtain air traffic services. It is described in \[10\], Page II-6-1 Section 6.   |

|**Provision**           |      |
|:---                    |:-----|
|**Provider**            | Example Service Provider |
|**Provider Description**| The Example Service Provider is a non-existent eASP used for illustration purposes.|
|**Provider Type**       | The Filing Service is provided by eASP(s).   |

|**Categorisation**                      |   |
|:--                                     |:---------------|
|**Service Type**                        | Swim compliant |
|**Life Cycle Stage**                    | Operational |
|**Business Activity Type**              | Demand and capacity balancing  |
|**Information Category**                | Cooperative network information exchange  |
|**Intended Consumer**                   | The Filing Service is consumed by eAU(s). |
|**Application Message Exchange Pattern**| Request Reply  |

|**Operational Need**|
|:-------------------|
| An eFPL should be filed in order to obtain air traffic services   |                                                                                                                                                       |                                                                                                                                                   |

|**Functionality**  |
|:----------------- |
| The ability to submit a filed flight plan and associated messages (Update, Cancel) and to provide the appropriate response messages (Submission Response, Filing Status) in accordance with FF-ICE procedures.|


|**Service Interface**                     |                                 |
|:---------------------------------------- |---------------------------------|
|**Name**                                  | Filing Provider eASP Interface  |
|**Description**                           | TBD                             |
|**Interface provision side**              | Provider side interface         |
|**TI primitive message exchange pattern** | Synchronous request response    |
|**Service interface binding**             | SWIM\_TI\_YP\_1\_0\_WS\_SOAP    |
|**Network interface binding**             | TI\_YP\_1\_0.IPV4\_UNICAST      |
	
|**Operation**          |                                  |
|:----------------------|:---------------------------------|
|**name**               | submitFlightPlan |
|**description**        | Request the submission of a new Flight Plan |
|**Idempotency**        | Idempotent |
|**Synchronicity**      | Synchronous |
|**TI Protocol method** | HTTP POST</ |
    
|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | flightPlanSubmissionRequest |
|**Description**        | <br><br> `MESSAGE TEMPLATE` [FficeFFP_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/preliminaryflightplan/fficemessage/FficeFFP_FficeMessage.xsd)|
|**Direction**          | In |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |
    
|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | flightPlanSubmissionReply |
|**Description**        | <br><br> `MESSAGE TEMPLATE` [FficeSR_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/preliminaryflightplan/fficemessage/FficeSR_FficeMessage.xsd)|
|**Direction**          | Out |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |

|**Operation**          |                                  |
|:----------------------|:---------------------------------|
|**name**               | updateFlightPlan |
|**description**        | Request the update of a Flight Plan |
|**Idempotency**        | Non idempotent |
|**Synchronicity**      | Synchronous |
|**TI Protocol method** | HTTP POST |
    
|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | flightPlanUpdateRequest |
|**Description**        | Request the update of a Flight Plan<br><br> `MESSAGE TEMPLATE` [FficeFPU_FficeMessage.xsd.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/preliminaryflightplan/fficemessage/FficeFPU_FficeMessage.xsd)|
|**Direction**          | In |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |
    
|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | flightPlanUpdateReply |
|**Description**        | Reply returned in response to flightPlanUpdateRequest<br><br> `MESSAGE TEMPLATE` [FficeSR_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/preliminaryflightplan/fficemessage/FficeSR_FficeMessage.xsd)|
|**Direction**          | Out |
|**Content Type**       | text/xml< |
|**Is fault**           | False |
|**Content Encoding**   |  |

|**Operation**          |                                  |
|:----------------------|:---------------------------------|
|**name**               | cancelFlightPlan |
|**description**        | Request the cancellation of a Flight Plan |
|**Idempotency**        | Non idempotent |
|**Synchronicity**      | Synchronicity |
|**TI Protocol method** | HTTP POST |
    
|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | flightPlanCancellationRequest |
|**Description**        | Request the cancellation of a Flight Plan<br><br> `MESSAGE TEMPLATE` [FficeFC_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/preliminaryflightplan/fficemessage/FficeFC_FficeMessage.xsd)|
|**Direction**          | In |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |
    
|**Operation message**  |                                  |
|:----------------------|:---------------------------------|
|**Name**               | flightPlanCancellationReply |
|**Description**        | Reply returned in response to preliminaryFlightPlanCancellationRequest<br><br> `MESSAGE TEMPLATE` [FficeSR_FficeMessage.xsd](https://www.fixm.aero/releases/FFICE-Msg-1.0.0/schemas/applications/fficemessage/fficetemplates/preliminaryflightplan/fficemessage/FficeSR_FficeMessage.xsd)|
|**Direction**          | Out |
|**Content Type**       | text/xml |
|**Is fault**           | False |
|**Content Encoding**   |  |

Appendix XXX Section YYY provides detailed information regarding… TBD

***

`EXAMPLE`

> TODO
