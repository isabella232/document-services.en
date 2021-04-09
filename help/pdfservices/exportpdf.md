---
title: Leveraging PDF Tools API to export PDF to Word, PowerPoint, and more
description: Adobe PDF Tools API provides the ability to convert PDF files to MS Office, text, and images using APIs
type: tutorial
role: Developer
skill: Beginner
thumbnail: KT-6674.jpg
kt: 6674
exl-id: 55f5b04e-0249-47d9-9131-2f9ec01db7e8,f16f6964-23f4-4205-b4e9-c724551ca529
---

# Leveraging PDF Tools API to export PDF to Word, PowerPoint, and more

![Create PDF Hero Image](assets/ExportPDF_hero.jpg)

Adobe PDF Tools API provides the ability to convert PDF files to MS Office, text, and images using APIs. There are many common use cases to unlock existing PDFs for content editing and analysis and with PDF Tools API developers can easily integrate this capability into existing systems and applications. Convert PDF files to MS Word for editing of content, approvals, and later sending for signatures to create custom contract workflows. Or export PDF content to MS Excel format for invoice and financial calculations or data analysis.

The Export operation supports the following PDF file conversions:

* PDF to Microsoft Word (DOC, DOCX)
* PDF to Microsoft PowerPoint (PPTX)
* PDF to Microsoft Excel (XLSX)
* PDF to text (RTF)
* PDF to Image (JPEG, PNG)

In this tutorial we’ll cover the basics of how to run your first PDF Tools API export operation using sample files for Node.js, Java, and .Net languages.

## Step 1: First, create your credentials and setup your environment:

Use the getting started tutorials below to create your API credentials, download sample files, and setup your environment.

[Getting Started with PDF Tools API and Java](gettingstartedjava.md)
[Getting Started with PDF Tools API and .Net](gettingstartednet.md)
[Getting Started with PDF Tools API and Node.js](createpdffromhtml.md)

## Step 2: Run export pdf operation using the sample files

### Java

1. Open a command prompt

1. Change directories into your sample code directory

    E.g., C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples>

1. Run the following command:

    mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.exportpdf.ExportPDFToDOCX    

Your PDF will be created in the src/main/resources directory.

### .Net

1. Open a command prompt

1. Change directories into your sample code directory

    E.g., C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples>

1. Change directories again into the ExportPDFtoDocx directory

1. Run the following command:

    dotnet run ExportPDFToDocx.csproj

Your PDF will be created in the same directory.

## Node.js

1. Open a command prompt

1. Change directories into your sample code directory.

    E.g., C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples>

1. Run the following command:

    **node src/ocr/ocr-pdf.js**

Your PDF will be created in the location designated in the output, which by default is the pdfServicesSdkResult directory.

## Final thoughts:

You should now have a working example that can be imported into your existing applications to start a proof of concept. In each of the sample directories, you’ll also see another sample to export PDF files to image format. The same steps above will allow you to run that sample as well. To change to another format you can update the code to the new format you would like:

SupportedTargetFormats.PPTX

And the destination result:

output/exportPdfOutput.PPTX

To another format.

## Resources and next steps

For additional help and support, visit the Adobe [Document Services APIs](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&sort=latest_replies&filter=all) community forum

PDF Tools API [Documentation](https://www.adobe.com/go/pdftoolsapi_doc)

[FAQ](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) for PDF Tools API questions

[Contact us](https://www.adobe.com/go/pdftoolsapi_requestform) for questions on licensing and pricing
