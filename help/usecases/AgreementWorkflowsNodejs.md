---
title: Agreement Workflows with Adobe Document Services APIs in Node.js
description: Adobe Document Services APIs easily incorporate PDF capabilities into your web applications
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-7473.jpg
kt: 7473
keywords: Featured
exl-id: 44a03420-e963-472b-aeb8-290422c8d767
---
# Agreement workflows with Adobe Document Services APIs in Node.js

![Use Case Hero Banner](assets/UseCaseAgreementHero.jpg)

Many business applications and processes require documentation like proposals and agreements. PDF documents ensure that files are more secure and less modifiable. They also provide digital signature support so your clients can quickly and easily complete their documents. Adobe Document Services APIs easily incorporate PDF capabilities into your web applications.

## What you can learn

In this hands-on tutorial, learn how to add PDF services to a Node.js application to digitize an agreement process.

## Relevant APIs and resources

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF Embed API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html) 

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Project code](https://github.com/adobe/pdftools-node-sdk-samples)

## Setting up Adobe Document Services

To get started, set up credentials to use Adobe Document Services. Register an account and use the [Node.js Quickstart](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#node-js) to verify your credentials work before integrating the functionality into a larger application.

First, get an Adobe developer account. Then, on the [Get Started](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK) page, select the *Get Started* option under Create New Credentials. You can sign up for their free trial which provides 1,000 Document Transactions that can be used over six months.

![Image of Creating New Credentials](assets/AWNjs_1.png)

On the following Create New Credentials page, you are prompted to decide between the PDF Embed API and the PDF Services API.

Select *PDF Services API*.

Enter a name for the application and check the box labeled *Create personalized code sample*. Checking this box automatically embeds your credentials in the code sample. If you leave this box unchecked, you must manually add your credentials to the application.

Select *Node.js* for the application type and click *Create Credentials*.

A few moments later, a .zip file begins to download with a sample project including your credentials. The Node.js package for document services is already included as part of the sample project code.

![Image of Selecting PDF Services API Credentials](assets/AWNjs_2.png)

## Configuring the sample project manually

If you choose to not download a sample project from the Create New Credentials page, you can also set up the project manually.

Download the code (without your credentials embedded) from [GitHub](https://github.com/adobe/pdftools-node-sdk-samples). If you use this version of the code, you must add your credentials to the pdftools-api-credentials.json file before use:

```
{
  "client_credentials": {
    "client_id": "<client_id>",
    "client_secret": "<client_secret>"
  },
  "service_account_credentials": {
    "organization_id": "<organization_id>",
    "account_id": "<technical_account_id>",
    "private_key_file": "<private_key_file_path>"
  }
}
```

For your own application, you need to copy the private key file and credentials files to your application source.

You must install the Node.js package for document services. To install the package, use the following command:

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

## Setting up logging

The samples here use Express for the application framework. They also use log4js for application logging. With log4js, you can easily direct logging to the console or out to a file:

```
const log4js = require('log4js');
const logger = log4js.getLogger();
log4js.configure( {
	appenders: { fileAppender: { type:'file', filename: './logs/applicationlog.txt'}},
	categories: { default: {appenders: ['fileAppender'], level:'info'}}
});
 
logger.level = 'info';
logger.info('Application started')
```

The above code writes logged data to a file in ./logs/applicationlog.txt. If you want it to write to the console instead, you can comment out the call to log4js.configure.

## Converting Word files to PDF

Agreements and proposals are often written in a productivity application, like Microsoft Word. To accept documents in this format and convert the document to PDF, you can add functionality for your application. Let’s look at how to upload and save a document in an Express application and save it to the file system.

In the application’s HTML, add a file element and a button for starting the upload:

```
<input type="file" name="source" id="source" />
<button onclick="upload()" >Upload</button>
```

In the page’s JavaScript, upload the file asynchronously using the fetch function:

```
function upload()
{
  let formData = new FormData();
  var selectedFile = document.getElementById('source').files[0];
  formData.append("source", selectedFile);
  fetch('documentUpload', {method:"POST", body:formData});
}
```

Choose a folder to accept your uploaded files. The application needs a path to this folder. Find the absolute path by using a relative path joined with \_\_dirname:

```
const uploadFolder = path.join(__dirname, "../uploads");
```

Since the file is submitted via post, you must respond to a post message on the server side:

```
router.post('/', (req, res, next) => {
  console.log('uploading')
  if(!req.files || Object.keys(req.files).length === 0) {
  return res.status(400).send('No files were uploaded');
  }
	
  const uploadPath = path.join(uploadFolder, req.files.source.name);
  var buffer = req.files.source.data;
  var result = {"success":true};
  fs.writeFile(uploadPath, buffer, 'binary', (err)=> {
    if(err) {
      result.success = false;
    }
    res.json(result);
  });       
});
```

After this function executes, the file saves in the applications upload folder and is available for further processing.

Next, convert the file from its native format to PDF. The sample code you downloaded earlier contains a script named `create-pdf-from-docx.js` for converting a document to PDF. The following function, `convertDocumentToPDF`, takes an uploaded document and converts it to a PDF in a different folder:

```
function convertDocumentToPDF(sourcePath, destinationPath)
{	
  try {   
    const credentials = PDFToolsSDK.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdftools-api-credentials.json")
    .build();
 
    const executionContext = 
      PDFToolsSDK.ExecutionContext.create(credentials),
    createPdfOperation = PDFToolsSDK.CreatePDF.Operation.createNew();
 
    const docxReadableStream = fs.createReadStream(sourcePath);
    const input = PDFToolsSDK.FileRef.createFromStream(
      docxReadableStream, 
      PDFToolsSDK.CreatePDF.SupportedSourceFormat.docx);
    createPdfOperation.setInput(input);
 
    createPdfOperation.execute(executionContext)
    .then(result => result.saveAsFile(destinationPath))
    .catch(err => {    	
      logger.erorr('Exception encountered while executing operation');        
    })
  }
  catch(err) {    	
    logger.error(err);
  }
}
```

You may notice a general pattern with the code:

The code builds a credentials object and an execution context, initializes some operation, then executes the operation with the execution context. You can see this pattern throughout the sample code.

By making a few additions to the upload function so it calls this function, the Word documents that users upload are now automatically converted to PDF.

The following code builds the destination path for the converted PDF and initiates the conversion:

```
const documentFolder = path.join(__dirname, "../docs");
var extPosition = req.files.source.name.lastIndexOf('.') - 1;
if(extPosition < 0 ) {
  extPosition = req.files.source.name.length
}
const destinationName = path.join(documentFolder,  
  req.files.source.name.substring(0, extPosition) + '.pdf');
console.log(destinationName);
 
logger.info('converting to ${destinationName}')
  convertDocumentToPDF(uploadPath, destinationName);
```

## Converting other file types to PDF

The document toolkit converts other formats to PDF, such as static HTML, another common document type. The toolkit accepts HTML documents packaged as a .zip file with all resources referenced by the document (CSS files, images, and other files) in the same .zip file. The HTML document itself must be named index.html and placed in the root of the .zip file.

To convert a .zip file containing HTML, use the following code:

```
//Create an HTML to PDF operation and provide the source file to it
htmlToPDFOperation = PDFToolsSdk.CreatePDF.Operation.createNew(); 	
const input = PDFToolsSdk.FileRef.createFromLocalFile(sourceZipFile);
htmlToPDFOperation.setInput(input);
 
// custom function for setting options
setCustomOptions(htmlToPDFOperation);
 
// Execute the operation and Save the result to the specified location.
htmlToPDFOperation.execute(executionContext)
  .then(result => result.saveAsFile(destinationPdfFile))
  .catch(err => {
    logger.error('Exception encountered while executing operation');
});
```

The function `setCustomOptions` specifies other PDF settings, such as the page size. Here, you can see the function sets the page size to 11.5 by 11 inches:

```
const setCustomOptions = (htmlToPDFOperation) => {    
  const pageLayout = new PDFToolsSdk.CreatePDF.options.PageLayout();
  pageLayout.setPageSize(11.5, 8);

  const htmlToPdfOptions = 
    new PDFToolsSdk.CreatePDF.options.html.CreatePDFFromHtmlOptions.Builder()
    .includesHeaderFooter(true)
    .withPageLayout(pageLayout)
    .build();
  htmlToPDFOperation.setOptions(htmlToPdfOptions);
};
```

Opening an HTML document containing some terms, you get the following within the browser:

![Image of Computer Terms](assets/AWNjs_3.png)

The source for this document is composed of a CSS file and an HTML file:

![Image of CSS and HTML file](assets/AWNjs_4.png)

After processing the HTML file, you have the same text in PDF format:

![PDF file of Computer Terms](assets/AWNjs_5.png)

## Appending pages

Another common operation with PDF files is appending pages to the end that may have standard text, such as a list of terms. The document toolkit can combine several PDF documents into a single document. If you have a list of document paths (here in `sourceFileList`), you can add each file’s file references to an object for a combine operation.

When the combine operation executes, it provides a single file with of the source content. You can use `saveAsFile` on the object to persist the file to storage.

```
const executionContext = PDFToolsSDK.ExecutionContext.create(credentials);
var combineOperation = PDFToolsSDK.CombineFiles.Operation.createNew();
 
sourceFileList.forEach(f => {
  var combinedSource = PDFToolsSDK.FileRef.createFromLocalFile(f);
  console.log(f);
  combineOperation.addInput(combinedSource);
});
	
 
combineOperation.execute(executionContext)
  .then(result=>result.saveAsFile(destinationFile))
  .catch(err => {
    logger.error(err.message);
});	
```

## Displaying PDF documents

You’ve performed several operations on PDF files, but ultimately, your user must view the documents. You can embed the document into a webpage using Adobe’s PDF Embed API.

On the page that displays the PDF, add a `<div />` element to hold the document and give it an ID. You use this ID shortly. In the web page, include a `<script />` reference to the Adobe JavaScript library:

```
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

The last bit of code you need is a function that displays the document once the Adobe PDF Embed API JavaScript is loaded. When you receive notification that the script is loaded through an adobe_dc_view\_sdk.ready event, create a new AdobeDC.View object. This object needs your Client ID and the ID of the element created earlier. Find your client ID in the [Adobe Developer Console](https://console.adobe.io/). When you view the settings for the application you created when generating credentials earlier, the Client ID displays there.

![Image of API Client Key](assets/AWNjs_6.png)

## Other PDF options

The [Adobe PDF Embed API demo](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf) enables you to preview the various other options for embedding PDF documents.

![Image of embedding PDF options ](assets/AWNjs_7.png)

You can turn various options on and off and immediately see how they render. When you find a combination you like, click the *\</\> Generate Code* button to generate the actual HTML code using those options.

![Image of Code Preview](assets/AWNjs_8.png)

## Adding digital signatures and security

Once a document is ready, you can add in digital signatures for approval using Adobe Sign. This functionality works a bit differently than the functionality you have used so far. For digital signatures, an application must be configured to use OAuth for user authentication.

The first step in setting up your application is to [register your application](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/gstarted/create_app.md) to use OAuth for Adobe Sign. Once signed in, navigate to the screen for creating applications by clicking on *Account*, then open the *Adobe Sign API* section, and click *API Applications* to open the list of registered applications.

![Image of first step in registering your application](assets/AWNjs_9.png)

To create a new application entry, click on the plus icon in the upper-right corner.

![Image of the plus icon in upper-right corner of screen](assets/AWNjs_10.png)

In the window that opens, enter an application name and display name. Select *Customer* for the domain, then click *Save*.

![Image of where to enter application name and display name](assets/AWNjs_11.png)

After the application is created, you can select it from the list and click on *Configure OAuth for Application*. Select the options. In the Redirect URL, enter the URL for your application. You can enter multiple URLs here. For the application you’re testing, the value is:

```
http://localhost:3000/signed-in 
```

The process of using OAuth to obtain a token is standard. Your application directs a user to a URL for signing in. After the user successfully signs in,
they are redirected back to the application with additional information in the page’s query parameters.

For the login URL, your application must pass your Client ID, redirect URL, and a list of the scopes needed.

The pattern for the URL looks like the following:

```
https://secure.adobesign.com/public/oauth?
  redirect_uri=&
  response_type=code&
  client_id=&
  scope=
```

The user is prompted to sign into their ID for Adobe Sign. After signing in, they are asked whether to provide permissions to the application.

![Image of confirm access screen](assets/AWNjs_12.png)

If the user clicks *Allow Access* on the redirect URL, a query parameter named code passes the authorization code:

https://YourServer.com/?code=**\<authorization_code\>**\&api_access_point=https://api.adobesign.com&web_access_point=https://secure.adobesign.com

Posting this code to the Adobe Sign server along with your client ID and client secret provides an access token to access the service. Save the values in the parameters `api_access_point` and `web_access_point`. These values are used for further requests.

```
var requestURL = ' ${api_access_point}oauth/token?code=${code}'
  +'&client_id=${client_id}'
  +'&client_secret=${client_secret}&'
  +'redirect_uri=${redirect_url}&'
  +'grant_type=authorization_code';
request.post(requestURL, {form: { }
}, (err,response,body)=>{            	
    var token_response = JSON.parse(body)
    var access_token = token_response.access_token;
    console.log(access_token);
});
```

When a document requires a signature, the document must first be uploaded. Your application can upload the document to the `api_access_point` value that was received while requesting the OAUTH token. The endpoint is `/api/rest/v6/transientDocuments`. The request data looks like the following:

```
POST /api/rest/v6/transientDocuments HTTP/1.1
Host: api.na1.adobesign.com
Authorization: Bearer MvyABjNotARealTokenHkYyi
Content-Type: multipart/form-data
Content-Disposition: form-data; name=";File"; filename="MyPDF.pdf"
<PDF CONTENT>
```

Within your application, build the request with the following code:

```
var uploadRequest = {
  'method': 'POST',
  'url': '${oauthParameters.signin_domain}/api/rest/v6/transientDocuments',
  'headers': {
    'Authorization': 'Bearer  ${auth_token}'
  },
  formData: {
    'File': {
      'value': fs.createReadStream(documentPath),
      'options': {
        'filename': fileName,
        'contentType': null
      }
    }
  }
};
 
request(uploadRequest, (error, response) => {
  if (error) throw new Error(error);
  var jsonResponse = JSON.parse(response.body);
  var transientDocumentId = jsonResponse.transientDocumentId;
  logger.info('transientDocumentId:', transientDocumentId)
});
```

The request returns a `transientID` value. The document has been uploaded, but is not sent yet. To send the document, use the `transientID` to request to send the document.

Start by building a JSON object containing the information for the document to be signed. In the following, the variable `transientDocumentId` contains the ID from the above code and `agreementDescription` contains text describing the agreement needing signature. The people to sign the document are listed in `participantSetsInfo` by their email address and role.

```
var requestBody = {
  "fileInfos":[
    {"transientDocumentId":transientDocumentId}],
    "name":agreementDescription,
    "participantSetsInfo":[
      {"memberInfos":[{"email":"user@domain.com"}],
       "order":1,"role":"SIGNER"}
    ],
    "signatureType":"ESIGN","state":"IN_PROCESS"
};
```

Sending this web request builds the signing request and returns a JSON object with an ID for the agreement request:

```
request(requestBody, function (error, response) {
  if (error) throw new Error(error);
  var JSONResponse = JSON.parse(response.body);
  var requestId = JSONResponse.id;
});
```

If the signers forget to sign and need another notification email, send the notifications again using the ID received earlier. The only difference is that you also must add the participant IDs of the parties. You can get the participant IDs by sending a GET request to `/agreements/{agreementID}/members`.

To request to send the reminder, first build a JSON object describing the request. The minimal object needs a list of the participant IDs and a status for the reminder (“ACTIVE”, “COMPLETE”, or “CANCELLED”).

The request can optionally have additional information, such as a value for “note” that will display to the user. Or, a delay (in hours) to wait until sending the reminder (in `firstReminderDelay`), and a reminder frequency (in the field “frequency”), which accepts values such as DAILY_UNTIL_SIGNED, EVERY_THIRD_DAY_UNTIL_SIGNED, or WEEKLY_UNTIL_SIGNED.

```
var requestBody = {
  //participantList is an array of participant ID strings
  "recipientParticipantIds":participantList
  ,"status":"ACTIVE",
  "note":"This is a reminder to sign out important agreement."
}
 
var reminderRequest = {
  'method': 'POST',
  'url': '${oauthParameters.signin_domain}/api/rest/v6/agreements/${agreementID}/reminders',
  'headers': {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(requestBody)
 
};

request(reminderRequest, function (error, response) {
});
```

And that's all it takes to send a reminder request.

![Image of Web Form](assets/AWNjs_13.png)

## Creating Web Forms

You can also use the Adobe Sign API to create Web Forms. Web Forms enable you to embed a form within a web page or link directly to it. Once a Web Form is created, it also displays among the Web Forms in your Adobe Sign console. You can create Web Forms with DRAFT status for incremental building, AUTHORING status for editing the web form fields, and ACTIVE status to immediately host the form.

![Image of Web Form in the Adobe Sign Manage screen](assets/AWNjs_14.png)

To create a Web Form, use the form `transientDocumentId`. Decide on a title for the form and a status to initialize it.

```
var requestBody = {
  "fileInfos": [
    {
      "transientDocumentId": transientDocumentId
    }
  ],
  "name": webFormTitle,
  "state": status,
  "widgetParticipantSetInfo": {
    "memberInfos": [ { "email": "" } ],
    "role": "SIGNER"
  }
}
```

```
var createWebFormRequest = {
  'method': 'POST',
  'url': `${oauthParameters.signin_domain}/api/rest/v6/widgets`,
  'headers': {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(requestBody)
}
```

```
request(createWebFormRequest, function (error, response) {
  var jsonResp = JSON.parse(response.body);
  var webFormID = jsonResp.id;
});
```

You can now embed or link to your document.

## Next steps

As you can see from the quick starts and the provided code, it is easy to implement PDF and digital document approval processes using Node with the Adobe Document Services APIs. Adobe’s APIs integrate into your existing client applications seamlessly.

To discover the required scopes for a call, or to see how the call is built, you can build sample calls from the [Rest API documentation](https://secure.na4.adobesign.com/public/docs/restapi/v6). The [Quickstarts](https://github.com/adobe/pdftools-node-sdk-samples) also demonstrate other functionality and file formats the Adobe Document Services APIs processes.

You can add a multitude of PDF capabilities to your applications, enabling your users to quickly and easily view and sign their documents and much more. To start, check out [Adobe Document Services](https://www.adobe.io/apis/documentcloud/dcsdk/) today.
