---
title: "Interpreting ErrorRecord Objects | Microsoft Docs"
ms.custom: ""
ms.date: "09/13/2016"
ms.reviewer: ""
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 2a65b964-5bc6-4ade-a66b-b6afa7351ce7
caps.latest.revision: 9
---
# Interpreting ErrorRecord Objects

In most cases, an [System.Management.Automation.Errorrecord](/dotnet/api/System.Management.Automation.ErrorRecord) object represents a non-terminating error generated by a command or script. Terminating errors can also specify the additional information in an ErrorRecord via the [System.Management.Automation.Icontainserrorrecord](/dotnet/api/System.Management.Automation.IContainsErrorRecord) interface.

If you want to write an error handler in your script or a host to handle specific errors that occur during command or script execution, you must interpret the [System.Management.Automation.Errorrecord](/dotnet/api/System.Management.Automation.ErrorRecord) object to determine whether it represents the class of error that you want to handle.

When a cmdlet encounters a terminating or non-terminating error, it should create an error record that describes the error condition. The host application must investigate these error records and perform whatever action will mitigate the error. The host application must also investigate error records for nonterminating errors that failed to process a record but were able to continue, and it must investigate error records for terminating errors that caused the pipeline to stop.

> [!NOTE]
> For terminating errors, the cmdlet calls the [System.Management.Automation.Cmdlet.Throwterminatingerror*](/dotnet/api/System.Management.Automation.Cmdlet.ThrowTerminatingError) method. For non-terminating errors, the cmdlet calls the [System.Management.Automation.Cmdlet.Writeerror*](/dotnet/api/System.Management.Automation.Cmdlet.WriteError) method.

## Error Record Design

Error records are designed to provide additional error information that is not available in exceptions while ensuring that the combined information in each error record is unique. This uniqueness allows the host application to inspect the different parts of the error record so that it can identify the error condition and the action the host must take.

## Interpreting Error Records

You can review several parts of the error record to identify the error. These parts include the following:

- The error category

- The error exception

- The fully qualified error identifier (FQID)

- Other information

### The Error Category

The error category of the error record is one of the predefined constants provided by the [System.Management.Automation.Errorcategory](/dotnet/api/System.Management.Automation.ErrorCategory) enumeration. This information  is available through the [System.Management.Automation.Errorrecord.Categoryinfo*](/dotnet/api/System.Management.Automation.ErrorRecord.CategoryInfo) property of the [System.Management.Automation.Errorrecord](/dotnet/api/System.Management.Automation.ErrorRecord) object.

The cmdlet can specify the CloseError, OpenError, InvalidType, ReadError, and WriteError categories, and other error categories. The host application can use the error category to capture groups of errors.

### The Exception

The exception included in the error record is provided by the cmdlet and can be accessed through the [System.Management.Automation.Errorrecord.Exception*](/dotnet/api/System.Management.Automation.ErrorRecord.Exception) property of the [System.Management.Automation.Errorrecord](/dotnet/api/System.Management.Automation.ErrorRecord) object.

Host applications can use the `is` keyword to identify that the exception is of a specific class or of a derived class. It is better to branch on the exception type, as shown in the following example.

`if (MyNonTerminatingError.Exception is AccessDeniedException)`

This way, you catch the derived classes. However, there are problems if the exception is deserialized.

### The FQID

The FQID is the most specific information you can use to identify the error. It is a string that includes a cmdlet-defined identifier, the name of the cmdlet class, and the source that reported the error. In general, an error record is analogous to an event record of a Windows Event log. The FQID is analogous to the following tuple, which identifies the class of the event record: (*log name*, *source*, *event ID*).

The FQID is designed to be inspected as a single string. However, cases exist in which the error identifier is designed to be parsed by the host application. The following example is a well-formed fully qualified error identifier.

`CommandNotFoundException,Microsoft.PowerShell.Commands.GetCommandCommand.`

In the previous example, the first token is the error identifier, which is followed by the name of the cmdlet class. The error identifier can be a single token, or it can be a dot-separated identifier that allows for branching on inspection of the identifier. Do not use white space or punctuation in the error identifier. It is especially important not to use a comma; a comma is used by Windows PowerShell to separate the identifier and the cmdlet class name.

### Other Information

The [System.Management.Automation.Errorrecord](/dotnet/api/System.Management.Automation.ErrorRecord) object might also provide information that describes the environment in which the error occurred. This information includes items such as error details, invocation information, and the target object that was being processed when the error occurred. Although this information might be useful to the host application, it is not typically used to identify the error. This information is available through the following properties:

[System.Management.Automation.Errorrecord.Errordetails*](/dotnet/api/System.Management.Automation.ErrorRecord.ErrorDetails)

[System.Management.Automation.Errorrecord.Invocationinfo*](/dotnet/api/System.Management.Automation.ErrorRecord.InvocationInfo)

[System.Management.Automation.Errorrecord.Targetobject*](/dotnet/api/System.Management.Automation.ErrorRecord.TargetObject)

## See Also

[System.Management.Automation.Errorrecord](/dotnet/api/System.Management.Automation.ErrorRecord)

[System.Management.Automation.Errorcategory](/dotnet/api/System.Management.Automation.ErrorCategory)

[System.Management.Automation.Errorcategoryinfo](/dotnet/api/System.Management.Automation.ErrorCategoryInfo)

[System.Management.Automation.Cmdlet.Writeerror*](/dotnet/api/System.Management.Automation.Cmdlet.WriteError)

[System.Management.Automation.Cmdlet.Throwterminatingerror*](/dotnet/api/System.Management.Automation.Cmdlet.ThrowTerminatingError)

[Adding Non-Terminating Error Reporting to Your Cmdlet](./adding-non-terminating-error-reporting-to-your-cmdlet.md)

[Windows PowerShell Error Reporting](./error-reporting-concepts.md)

[Writing a Windows PowerShell Cmdlet](./writing-a-windows-powershell-cmdlet.md)