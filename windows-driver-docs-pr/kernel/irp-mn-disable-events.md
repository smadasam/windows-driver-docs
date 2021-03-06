---
title: IRP\_MN\_DISABLE\_EVENTS
author: windows-driver-content
description: Any WMI driver that registers one or more event blocks must handle this IRP.
ms.author: windowsdriverdev
ms.date: 08/12/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-devices
ms.assetid: 3187643b-27d7-4a6d-8fbe-4f8eb6c251ed
keywords:
 - IRP_MN_DISABLE_EVENTS Kernel-Mode Driver Architecture
---

# IRP\_MN\_DISABLE\_EVENTS


Any WMI driver that registers one or more event blocks must handle this IRP. A driver can handle WMI IRPs either by calling [**WmiSystemControl**](https://msdn.microsoft.com/library/windows/hardware/ff565834) or by handling the IRP itself, as described in [Handling WMI Requests](https://msdn.microsoft.com/library/windows/hardware/ff546968).

If a driver calls [**WmiSystemControl**](https://msdn.microsoft.com/library/windows/hardware/ff565834) to handle an **IRP\_MN\_DISABLE\_EVENTS** request, WMI in turn calls that driver's [*DpWmiFunctionControl*](https://msdn.microsoft.com/library/windows/hardware/ff544094) routine.

Major Code
----------

[**IRP\_MJ\_SYSTEM\_CONTROL**](irp-mj-system-control.md)
When Sent
---------

WMI sends this IRP to inform the driver that a data consumer has requested no further notification of an event.

WMI sends this IRP at IRQL = PASSIVE\_LEVEL in an arbitrary thread context.

## Input Parameters


**Parameters.WMI.ProviderId** points to the device object of the driver that should respond to the request. This pointer is located in the driver's I/O stack location in the IRP.

**Parameters.WMI.DataPath** points to a GUID that identifies the event block to disable.

## Output Parameters


None.

## I/O Status Block


If the driver handles the IRP by calling [**WmiSystemControl**](https://msdn.microsoft.com/library/windows/hardware/ff565834), WMI sets **Irp-&gt;IoStatus.Status** and **Irp-&gt;IoStatus.Information** in the I/O status block.

Otherwise, the driver sets **Irp-&gt;IoStatus.Status** to STATUS\_SUCCESS or to an appropriate error status such as the following:

STATUS\_WMI\_GUID\_NOT\_FOUND

STATUS\_INVALID\_DEVICE\_REQUEST

On success, a driver sets **Irp-&gt;IoStatus.Information** to zero.

Operation
---------

A driver can handle WMI IRPs either by calling [**WmiSystemControl**](https://msdn.microsoft.com/library/windows/hardware/ff565834) or by handling the IRP itself, as described in [Handling WMI Requests](https://msdn.microsoft.com/library/windows/hardware/ff546968).

If a driver handles WMI IRPs by calling [**WmiSystemControl**](https://msdn.microsoft.com/library/windows/hardware/ff565834), that routine calls the driver's [*DpWmiFunctionControl*](https://msdn.microsoft.com/library/windows/hardware/ff544094) routine, or returns STATUS\_SUCCESS if the driver does not define the routine.

If a driver handles an **IRP\_MN\_DISABLE\_EVENTS** request itself, it should do so only if **Parameters.WMI.ProviderId** points to the same device object as the pointer that the driver passed to [**IoWMIRegistrationControl**](https://msdn.microsoft.com/library/windows/hardware/ff550480). Otherwise, the driver must forward the request to the next-lower driver.

Before handling a request, the driver must determine whether **Parameters.WMI.DataPath** points to a GUID the driver supports. If not, the driver must fail the IRP and return STATUS\_WMI\_GUID\_NOT\_FOUND.

If the driver supports the event block, it disables the event for all instances of that block.

It is unnecessary for the driver to check whether events are already disabled for the event block because WMI sends a single disable request for that event block when the last data consumer disables the event. WMI will not send another disable request without an intervening request to enable.

For details about defining event blocks, see [Designing WMI Data and Event Blocks](https://msdn.microsoft.com/library/windows/hardware/ff543036).

Requirements
------------

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p>Header</p></td>
<td>Wdm.h (include Wdm.h, Ntddk.h, or Ntifs.h)</td>
</tr>
</tbody>
</table>

## See also


[*DpWmiFunctionControl*](https://msdn.microsoft.com/library/windows/hardware/ff544094)

[**IoWMIRegistrationControl**](https://msdn.microsoft.com/library/windows/hardware/ff550480)

[**IRP\_MN\_ENABLE\_EVENTS**](irp-mn-enable-events.md)

[**WMILIB\_CONTEXT**](https://msdn.microsoft.com/library/windows/hardware/ff565813)

[**WmiSystemControl**](https://msdn.microsoft.com/library/windows/hardware/ff565834)

 

 


--------------------
[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20%5Bkernel\kernel%5D:%20IRP_MN_DISABLE_EVENTS%20%20RELEASE:%20%288/10/2017%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")


