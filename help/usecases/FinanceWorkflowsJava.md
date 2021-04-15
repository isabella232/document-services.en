---
title: Managing Financial Document Workflows with Adobe Document Services APIs in Java
description: Adobe Document Services provides all the necessary tools, services, and features to process and extract data from PDF financial documents
type: Tutorial
role: Developer
skill: Beginner
thumbnail: KT-7482.jpg
kt: 7482
exl-id: 3bdc2610-d497-4a54-afc0-8b8baa234960
---
# Managing financial document workflows with Adobe Document Services APIs in Java

The financial industry uses PDF files extensively to exchange data because it helps maintain document format, design, and structure. This allows financial analysts and advisors to help their clients make well-informed decisions.

The PDF format, however, can be challenging to process and automate, especially when you must combine multiple data sources — a common use case in the financial industry. Building a custom solution to process PDF documents is an option, but there is no need to invest too much time and money in software and infrastructure. Adobe Document Services provides all the necessary tools, services, and features to process and extract data from PDF documents.

In this article, we will show you how you can use Adobe Document Services APIs for Java Spring Boot applications and build a model-view-controller (MVC) app that extracts content from PDF documents, converts it to other data formats such as Excel, combines multiple PDFs, and password protects the resources. By the end of this article, you will know how to process PDF documents and show them on your websites using Adobe’s [PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html).

## Setup

Adobe Document Services uses an authentication system to control resource access. To access the services, you must request an API key from Adobe for your organization or application. If you have an API key, continue to the next section. If you want to create a new API key, visit [Getting Started](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) in the Document Services site. You can create a key using their free trial which provides 1,000 Document Transactions that can be used for up to 6 months.

Note that to follow along with this article, you will need two sets of API keys:

-   For Adobe PDF Tools — used to process the PDF document

-   For Adobe PDF Embed API

After creating the credentials, you can copy the PDF Tools API credentials and the private key to the Spring Boot application inside the resources section. You can learn more about the [Maven and Gradle libraries and dependencies](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services) using the Adobe Document Services website. Make sure you set up all necessary packages and libraries before you proceed.

![Screenshot of the directory location for PDF Tools API credentials](assets/FAWJ_1.png)

To configure the logging services, visit [Adobe documentation](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services) and scroll to the Logging section.

>[!NOTE]
>
> In your production environment, do not save the private keys in version control. Always use a secret vault or a key injection service to prevent unauthorized use of credentials.

Now that our Spring Boot application is configured, we can proceed with processing the PDFs and generating reports for customers.

## Submitting the report data

To use the Adobe PDF Tools API, first set up an `ExecutionContext` that consumes the credentials you provide. Since we have the credentials inside our application, we can read them from the file and create the context, as:

```
Credentials credentials = Credentials.serviceAccountCredentialsBuilder()
    .fromFile(AUTH_FILE_PATH)
    .build();

ExecutionContext executionContext = ExecutionContext.create(credentials);
```

Next, get the context to process the PDF documents. Here are the actions we can perform:

-   Convert the PDF documents (to Excel, Word, or graphics type)

-   Create the PDF documents (from HTML, Excel, Word, and more)

-   Combine multiple PDF documents

-   Protect and unprotect the PDF documents (you need to have the password)

-   Optimize the PDF documents for delivery on networks

All these samples are available in the [GitHub samples](https://github.com/adobe/pdftools-java-sdk-samples/tree/master/src/main/java/com/adobe/platform/operation/samples) repository.

Next, in Spring Boot, you can get a file using the String path or the Stream where the file is being uploaded. Every operation that you perform needs to be initialized and an input file path must be set. For this article, we use the publicly available PDF reports from [Blackrock](https://www.blackrock.com/us/individual/products/investment-funds). You can use any other source including your own reports.

We start by capturing the [FileRef](https://www.adobe.com/devnet-docs/dcsdk_io/servicesSDK/javadoc/com/adobe/platform/operation/io/FileRef.html) object from the file. Note that, for simplicity, we focus on the files by String path. Below, we create an operation to convert a file in our path from PDF to Excel:

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
ExportPDFOperation exportOperation = ExportPDFOperation.createNew(ExportPDFTargetFormat.XLSX);

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_PDF);
exportOperation.setInput(inputPdf);
```

After this step, our program is ready to run the first operation on the PDF. Next, we execute the operation and get the result in the Excel sheet:

```
try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_EXCEL);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

This scenario handles only one PDF file. You could also start with multiple PDF files and combine them into a single file. Using multiple files is common in financial data reporting because you need to process funds from multiple sources to provide a comprehensive report.

## Generating the report

While Adobe Document Services does not support processing Excel documents out of the box, you can still use community frameworks and libraries to process the content.

For example, you can use the [Apache POI](https://poi.apache.org/) to process Excel (or other Microsoft documents) in your Java Spring Boot app, or you might perform other manual or automated tasks on the Excel file.

In this example, starting with our PDF documents, we will extract the net asset value for our three funds and show them in a table. You can pull other information as well, such as charts and tables, based on your requirements and the data available. You can even bring in data from other sources.

After your report is generated — in this example, in an Excel format — you can use Adobe PDF Tools operations to convert the report back to a PDF document and protect it.

To convert the report from Excel format to a PDF document, use the following operation:

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
CreatePDFOperation exportOperation = CreatePDFOperation.createNew();

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_EXCEL);
exportOperation.setInput(inputPdf);

try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_PDF);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

>[!TIP]
>
> To prevent having to recreate the object every time a request comes, we can use Spring’s dependency injection to inject the `ExecutionContext` object.

This code will generate a PDF document from the report we have in Excel format.

Before delivering this PDF to our customers, we can protect it with a password. We need to create another operation that handles this for us, [ProtectPDFOperation](https://www.adobe.com/devnet-docs/dcsdk_io/servicesSDK/javadoc/com/adobe/platform/operation/pdfops/ProtectPDFOperation.html), then use [ProtectPDFOptions](https://www.adobe.com/devnet-docs/dcsdk_io/servicesSDK/javadoc/com/adobe/platform/operation/pdfops/options/protectpdf/ProtectPDFOptions.html) to add the password to the document.

```
ProtectPDFOptions options = ProtectPDFOptions.passwordProtectOptionsBuilder()
                    .setUserPassword("p@55w0rd")
                    .setEncryptionAlgorithm(EncryptionAlgorithm.AES_256)
                    .build();
ProtectPDFOperation operation = ProtectPDFOperation.createNew(options);
```

Next, specify the input and execute the operation. The resulting file you receive should have a password on it to prevent unauthorized access.

## Displaying the report

Now that our PDF report is generated, we can display the report on our website using the Adobe PDF Embed API. This is a JavaScript API that enables web developers to load and render the PDF documents natively inside the web browser.

>[!NOTE]
>
> This is where you need the second credential token, the client ID.

In your Spring Boot application, add the following HTML snippet where you want to render the PDF report:

```
<div id="pdf-viewer"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
    document.addEventListener("adobe_dc_view_sdk.ready", function()
    {
        var adobeDCView = new AdobeDC.View({ clientId: "<your-client-id-here>", divId: "pdf-viewer" });
        adobeDCView.previewFile(
        {
            content: {
                location: {
                    url: "<your-document.pdf>"
                }
            },
            metaData: {
                fileName: "<document-name.pdf>"
            }
        });
    });
</script>
```

This script will load the PDF document and enable viewers to annotate and comment on the documents. Here is the view of this Embed API as shown in Firefox:

![Screenshot of a PDF document in Firefox](assets/FAWJ_2.png)

The PDF Embed API provides all the tools necessary to preview the PDF as well as to annotate the report.

## Conclusion

In this article, we explored the [Adobe Document Services](https://www.adobe.io/apis/documentcloud/dcsdk/) APIs and discussed how we can use these services to process PDF data and generate reports for financial decisions. We demonstrated how we can integrate the APIs into our systems, using Java Spring Boot as an example framework, to show how easy it is to quickly process PDF documents.

We encourage you to explore [Adobe Document Services](https://www.adobe.io/apis/documentcloud/dcsdk/) and see what Adobe PDF Toolkit can do for your business. To learn about more features available in the SDK, consult the [GitHub repository](https://github.com/adobe/pdftools-java-sdk-samples) for the samples, and explore how [PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) can help you quickly show PDFs inside your applications.

To easily combine and manipulate documents, creating helpful PDF reports for your financial clients, start by signing up for your free [Adobe developer account](https://www.adobe.io/apis/documentcloud/dcsdk/) today.
