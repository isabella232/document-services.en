---
title: Automate Legal Workflows
description: Learn how to modernize employee onboarding with Adobe Document Services APIs
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-10202.jpg
kt: 10202
---
# Automate legal workflows

![Use Case Hero Banner](assets/usecaseautomatelegalhero.jpg)

In a large organization, dealing with employee onboarding can be a large, and slow, process. Typically there is a mix of customized documentation along with boilerplate material that must be presented to a new employee and perhaps agreed to with a signature. All of this can spread across multiple different steps taking valuable time away from people in a process that is absolutely ripe for automation. Adobe Document Services and Acrobat Sign can modernize this approach and set up a level of automation that frees your HR personal for more important tasks. Let's take a look at how this can be achieved. 

## What are Adobe Document Services?
## What are Adobe Document Services?

[Adobe Document Services](https://developer.adobe.com/document-services/homepage) are a set of APIs related to working with documents (and not just PDFs). Broadly speaking, this suite of services falls into three main categories:

* First are the [PDF Services](https://developer.adobe.com/document-services/apis/pdf-services/) set of tools. These are "utility" methods for working with PDFs and other documents. It includes things like converting to and from PDF, performing OCR and optimization, merging and splitting PDFs, and so forth. It's the toolbox of document processing features.
* [PDF Extract API](https://developer.adobe.com/document-services/apis/pdf-extract/) uses powerful AI/ML techniques to analyze a PDF and return an incredible amount of detail about the contents. This includes the text, styling and positional information, and can also return tabular data in CSV/XLS format as well as retrieve images. 
* Lastly, [Document Generation API](https://developer.adobe.com/document-services/apis/doc-generation/) lets developers use Microsoft Word as a "template", mix with with their data (from any source) and generate dynamic personalized documents (PDF and Word). 

Developers can [sign up](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) and try all of these services with a free trial. The Document Services platform uses a REST-based API but also supports SDKs for Node, Java, .NET, and Python (Extract only at this time).

While not an API, developers can also make use of our free [PDF Embed API](https://developer.adobe.com/document-services/apis/pdf-embed/), which provides a consistent and flexible viewing experience of documents with your web pages.

## What is Acrobat Sign?

[Acrobat Sign](https://www.adobe.com/sign.html) is the world leader in electronic signature services. It lets you create documents that can be sent out for signing and supports a variety of workflows. It can support a process that requires multiple signatures. It can support a process that requires signatures and additional information as well. All of this is supported by a powerful dashboard with a flexible authoring system. 

As with Document Services, Acrobat Sign has a [free trial](https://www.adobe.com/sign.html#sign_free_trial) that lets developers test the signing process both via the dashboard as well as with an easy-to-use REST-based API. 

## An onboarding scenario

Let's consider a real-world scenario that demonstrates where Adobe's services can help. When a new employee is joining a company, they need customized information tailored to their role. Along with this, they need company-wide material as well. Finally, they need to demonstrate their acceptance of corporate policy by signing the documents. We can break this down into concrete steps:

* First we need a customized cover letter that greets the new employee by name. It should contain information about the employee's name, role, salary, and location. 
* The previous document needs to be combined with a PDF that contains basic, company-wide information (think various HR policies, benefits, etc)
* A final document needs to be added that asks for the employee's signature and date.
* All of the above should be presented as one document that is sent to the employee for signing.

Now let's go into details on how we can achieve this.

## Generating dynamic documents

Adobe's [Document Generation](https://developer.adobe.com/document-services/apis/doc-generation/) API lets developer create dynamic documents by using Microsoft Word, and a simple templating language, as a basis for generating PDFs and Word documents. Here's an example of how this works. 

We can begin with a Word document that has hard coded values. We can style this any way we want, include graphics, tables, and so forth. Here's the initial document.

![Initial Document](assets/s1a.png)

Document Generation works by adding "tokens" to a Word document that are replaced with your data. While these tokens can be entered manually, we have a [Microsoft Word add-in](https://developer.adobe.com/document-services/docs/overview/document-generation-api/wordaddin/) that makes this easier to use. Opening it up provides a tool for authors to define tags, or sets of data, that can be used in your document. 

![Document Tagger](assets/s2.png)

You can either upload JSON information from a local file, copy in JSON text, or select to continue with initial data. Doing so lets you define your tags in an ad hoc manner based on what your particular needs are. So for example, we need a tag for name, role, salary, and location. This can be done by using the "Create Tag" button:

![Defining a tag](assets/s3.png)

After defining the first tag, you can continue to define as many as you need:

![Defined tags](assets/s4.png)

With the tags defined, you can then select the text in your document and replace it with the tags where appropriate. Here we've done this for name, role, and salary.

![Tags](assets/s5.png)

Document Generation doesn't support just simple tags but also logical expressions as well. The second paragraph of the document has text that only applies to people in Louisiana. We can add a conditional expression by going into the Advanced tab of the Document Tagger and defining a condition. Here is how we define a simple equality condition, but note that numerical comparisons and other comparison types are supported as well.

![Condition](assets/s6.png)

This can then be inserted and wrapped around the paragraph:

![Condition in Doc](assets/s7.png)

To test how this works, select "Generate document". You will need to sign in with an Adobe ID the first time you do this. After signing in, default JSON is presented that can be manually edited. 

![Data](assets/s8.png)

A PDF will be generated that can then be viewed or downloaded.

![Generated](assets/s9.png)

While the Document Tagger let's you quickly design and test, once done and in production, you can use one of our SDKs to automate this process. While actual code will differ based on specific needs, here's an example of how this code would look in Node.js:

```js
 const PDFServicesSdk = require('@adobe/pdfservices-node-sdk');

const credentials =  PDFServicesSdk.Credentials
	.serviceAccountCredentialsBuilder()
	.fromFile("pdfservices-api-credentials.json")
	.build();

// Data would be dynamic...
let data = {
	"name":"Raymond Camden",
	"role":"Lead Developer",
	"salary":9000,
	"location":"Louisiana"
}

// Create an ExecutionContext using credentials.
const executionContext = PDFServicesSdk.ExecutionContext.create(credentials);

// Create a new DocumentMerge options instance.
const documentMerge = PDFServicesSdk.DocumentMerge,
	documentMergeOptions = documentMerge.options,
	options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);

// Create a new operation instance using the options instance.
const documentMergeOperation = documentMerge.Operation.createNew(options);

// Set operation input document template from a source file.
const input = PDFServicesSdk.FileRef.createFromLocalFile('documentMergeTemplate.docx');
documentMergeOperation.setInput(input);

// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
	.then(result => result.saveAsFile('documentOutput.pdf'))
	.catch(err => {
		if(err instanceof PDFServicesSdk.Error.ServiceApiError
			|| err instanceof PDFServicesSdk.Error.ServiceUsageError) {
			console.log('Exception encountered while executing operation', err);
		} else {
			console.log('Exception encountered while executing operation', err);
		}
	});
```

In brief, the code sets up credentials, creates an operation object and sets the input and options, and then calls the operation. Finally it saves the result out as a PDF. (Results can be output as Word as well.) 

Document Generation supports much more complex use cases of course, including the ability to have fully dynamic tables and images. See the [documentation](https://developer.adobe.com/document-services/docs/overview/document-generation-api/) for more.

## Performing PDF operations

The [PDF Services API](https://developer.adobe.com/document-services/apis/pdf-services/) provide a large set of "utility" operations for working with PDFs. These include:

* Creating PDFs from Office docs
* Exporting PDFs to Office docs
* Combining and splitting PDFs
* Applying OCR to PDFs
* Setting, removing, and modifying protection to PDFs
* Deleting, inserting, re-ordering, and rotating pages
* Optimizing PDFs via compression or linearization
* Getting PDF properties

For our scenario, we need to merge the result of the Document Generation call with a standard PDF. This is fairly simple with the SDKs. Here's an example of this in Node.js:

```js
const PDFServicesSdk = require('@adobe/pdfservices-node-sdk');
 
// Initial setup, create credentials instance.
const credentials = PDFServicesSdk.Credentials
	.serviceAccountCredentialsBuilder()
	.fromFile("pdfservices-api-credentials.json")
	.build();
 
// Create an ExecutionContext using credentials and create a new operation instance.
const executionContext = PDFServicesSdk.ExecutionContext.create(credentials),
	combineFilesOperation = PDFServicesSdk.CombineFiles.Operation.createNew();
 
// Set operation input from a source file.
const combineSource1 = PDFServicesSdk.FileRef.createFromLocalFile('documentOutput.pdf'),
	  combineSource2 = PDFServicesSdk.FileRef.createFromLocalFile('standardCorporate.pdf');

combineFilesOperation.addInput(combineSource1);
combineFilesOperation.addInput(combineSource2);
 
// Execute the operation and Save the result to the specified location.
combineFilesOperation.execute(executionContext)
	.then(result => result.saveAsFile('combineFilesOutput.pdf'))
	.catch(err => {
		if (err instanceof PDFServicesSdk.Error.ServiceApiError
			|| err instanceof PDFServicesSdk.Error.ServiceUsageError) {
			console.log('Exception encountered while executing operation', err);
		} else {
			console.log('Exception encountered while executing operation', err);
		}
	});
```

This will take our two PDFs, merge them, and save the result in a new PDF. Simple and easy! See the [docs](https://developer.adobe.com/document-services/docs/overview/pdf-services-api/) for examples of what can be done.

## The signing process

As the final stop in our onboarding process, we need to get the employee to sign an agreement stating that they've read the previous documents and agree to any and all policies defined within. [Acrobat Sign](https://www.adobe.com/sign.html) supports many different workflows and integrations, including a completely automated one via an [API](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html). Broadly speaking, we can accomplish this final portion of the scenario with the following.

First, design the document that includes the form that needs signing. There's multiple ways of doing this, including a visual designed in the Adobe Sign user dashboard. Another option is to use the Document Generation Word add-in to insert the tags for you. Here we've done this for requesting a signature and date.

![Document with Sign Tags](assets/s10)

This document can be saved as a PDF, and using the same method described above, can be joined with all the documents together to create one cohesive package that contains a personalized greeting, standard corporate documentation, and a final page fit for signing.

This template can be uploaded to the Acrobat Sign dashboard and then used for new agreements. By using the REST API, this document can then be sent to the prospective employee to request their signature.

![Signed doc](assets/s11.png)

## Experience it yourself

Everything described in this article can be tested yourself right now. The Adobe Document Services API [free trial](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) currently gives you one thousand free requests over a six month period. Acrobat Sign's [free trial](https://www.adobe.com/sign.html#sign_free_trial) will let you send watermarked agreements for testing purposes. Have questions? Our [support forum](https://community.adobe.com/t5/document-services-apis/ct-p/ct-Document-Cloud-SDK) is monitored by our developers and support folks every day. FInally, for more inspiration, be sure to catch our next [Paper Clips](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF) episode. We have a regular live meeting where we share news, demos, and talk with our customers. 