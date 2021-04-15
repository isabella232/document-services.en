---
title: Control Your PDF Online Experience and Gather Analytics
description: In this hands-on tutorial, you'll learn how to use the Adobe PDF Embed API to control appearance, enable collaboration, and gather analytics about how user’s interact with PDFs, including time spent on a page and searches
type: tutorial
role: Developer
skill: Intermediate
thumbnail: KT-7487
kt: 7487
exl-id: 64ffdacb-d6cb-43e7-ad10-bbd8afc0dbf4
---
# Control your PDF online experience and gather analytics

Does your organization post PDFs on your website? Learn how to use the Adobe PDF Embed API to control appearance, enable collaboration, and gather analytics about how user’s interact with PDFs, including time spent on a page and searches.

To begin this 4 part tutorial, click on *Getting started with PDF Embed API*.

<table style="table-layout:fixed">
<tr>
  <td>
    <a href="controlpdfexperience.md#part1">
        <img alt="Getting started with PDF Embed API" src="assets/ControlPDFPart1_thumb.png" />
    </a>
    <div>
    <a href="controlpdfexperience.md#part1"><strong>Getting started with PDF Embed API</strong></a>
    </div>
  </td>
  <td>
    <a href="controlpdfexperience.md#part2">
        <img alt="Adding PDF Embed API to a webpage" src="assets/ControlPDFPart2_thumb.png" />
    </a>
    <div>
    <a href="controlpdfexperience.md#part2"><strong>Adding PDF Embed API to a webpage</strong></a>
    </div>
  </td>
  <td>
   <a href="controlpdfexperience.md#part3">
      <img alt="Accessing Analytics APIs" src="assets/ControlPDFPart3_thumb.png" />
   </a>
    <div>
    <a href="controlpdfexperience.md#part3"><strong>Accessing Analytics APIs</strong></a>
    </div>
  </td>
  <td>
   <a href="controlpdfexperience.md#part4">
      <img alt="Add interactivity based on events" src="assets/ControlPDFPart4_thumb.png" />
   </a>
    <div>
    <a href="controlpdfexperience.md#part4"><strong>Add interactivity based on events</strong></a>
    </div>
  </td>
</tr>
</table>

## Getting started with PDF Embed API {#part1}

### Resources

* Tutorial resources [download](https://github.com/benvanderberg/adobe-pdf-embed-api-tutorial)
* Adobe ID [get one here](https://accounts.adobe.com/)
* Web server (Node JS, PHP, etc.)
* Working knowledge of HTML / JavaScript / CSS

### Applications

* A basic web server (Node)
* Visual Studio Code
* GitHub

### Getting Credentials

1. Go to the Adobe.io website: https://www.adobe.io/
1. Scroll down to Build engaging document experiences and click on the **Learn more** button. 

   ![Screenshot of the Learn more button](assets/ControlPDF_1.png)

1. This takes you to the Adobe Document Services home page. In the navigation bar click on **Get Started**.
1. You’ll see an option in **Get Started with Document Services APIs** to **Create New Credentials** or **Manage Existing Credentials**. Click on the **Get Started** button under **Create New Credentials**.

   ![Screenshot of Get Started button](assets/ControlPDF_2.png)

1. In the next window to create new credentials, choose the **PDF Embed API** radio button, then add a credential name of your choice and an application domain.

   >[!NOTE]
   >
   >These credentials can only be used on the application domain that is listed here. You can use any domain you choose.

   ![Screenshot of credentials](assets/ControlPDF_3.png)

1. When finished, click the **Create Credentials** button.
1. The final page of the wizard provides you with your client credential details. Leave this window open so you can come back to it and copy the Client ID (API Key) for later use. Click on the **View Documentation** button to go to the documentation with detailed information on how to use this API.

   ![Screenshot of create credentials button](assets/ControlPDF_4.png)

   ![Screenshot of view documentation](assets/ControlPDF_5.png)

## Adding PDF Embed API to a webpage {#part2}

In this hands-on tutorial, you'll learn how to easily embed PDF Embed API into a webpage. You'll do this by using the Adobe PDF Embed API online demo to create our code.

### Resources

We created code for you to utilize. While you can use your own code, demonstrations will be in the context of the tutorial resources. Download sample code [here](https://github.com/benvanderberg/adobe-pdf-embed-api-tutorial).

1. Go to the Adobe Document Services website: https://www.adobe.io/apis/documentcloud/dcsdk/

   ![Screenshot of Adobe Document Services website](assets/ControlPDF_6.png)

1. Click on APIs in the navigation bar, then go to the PDF Embed API page in the drop-down link.

   ![Screenshot of PDF Embed API dropdown](assets/ControlPDF_7.png)

1. Click on **Try the demo**. A new window pops up with the developer sandbox for PDF Embed API.

   ![Screenshot of Try the demo](assets/ControlPDF_8.png)

1. Here you can see the options for the different viewing modes. Click on the different viewing modes for Full Window, Sized Container, In-Line, and Lightbox.

   ![Screenshot of viewing modes](assets/ControlPDF_9.png)

1. Click **Full Window** viewing mode, then click the **Customize** button to toggle options on and off.

   ![Screenshot of Customize button](assets/ControlPDF_10.png)

1. Disable **Download** PDF option.
1. Click on the **Generate Code** button to see the code preview.
1. Go back to the Client Credentials window from Part 1 and copy the **Client ID** from that page.

   ![Screenshot of Client ID](assets/ControlPDF_11.png)

1. Go to your code editor and open the **Web -> resources -> js -> dc-config.js** file. You’ll see that the clientID variable is there. Paste your client credentials between the double quotes to set the clientID to your credential.
1. Next, go back to the developer sandbox code preview and copy the second line that has the Adobe script: 

   ```
   <script src=https://documentccloud.adobe.com/view-sdk/main.js></script>
   ```

   ![Screenshot of script](assets/ControlPDF_12.png)

1. Go to your code editor and open the **Web -> exercise -> index.html** file. Paste the script code into the `<head>` of the file on line 18 under the comment that says: **TODO: EXERCISE 1: INSERT EMBED API SCRIPT TAG**.

   ![Screenshot of where to paste script code](assets/ControlPDF_13.png)

1. Go back to the developer sandbox code preview and copy the first line of code that has: 

   ```
   <div id=”adobe-dc-view”></div>
   ```

   ![Screenshot of where to copy code](assets/ControlPDF_14.png)

1. Go to your code editor and open the **Web -> exercise -> index.html** file again. Paste the `<div>` code into the `<body>` of the file on line 67 under the comment that says **TODO: EXERCISE 1: INSERT PDF EMBED API CODE**.


   ![Screenshot of where to paste code](assets/ControlPDF_15.png)

1. Go back to the developer sandbox code preview and copy the lines of code for the `<script>` below:

   ```
   <script type=”text/javascript”>
  	 document.addEventListener(“adobe_dc_view_sdk.ready”, 			function(){ 
	  	 var adobeDCView = new AdobeDC.View({clientId: 					“<YOUR_CLIENT_ID>”, divId: “adobe-dc-view”});
		   adobeDCView.previewFile({
			   content:{location: {url: “https://documentcloud.				adobe.com/view-sdk-demo/PDFs/Bodea Brochure.					pdf”}},
			   metaData:{fileName: “Bodea Brochure.pdf”}
	  	 }, {showDownloadPDF: false});
  	 });
   </script>
   ```

1. Go to your code editor and open the **Web -> exercise -> index.html** file again. Paste the `<script>` code into the `<body>` of the file on line 68 under the `<div>` tag.
1. Modify line 70 of the same **index.html** file to include the clientID variable that was created in step 8. 

   ![Screenshot of line 70](assets/ControlPDF_16.png)

1. Modify line 72 of the same **index.html** file to update the location of the PDF file to use a local file. There is one available in the tutorial files in: **/resources/pdfs/whitepaper.pdf**.
1. Save your modified files and preview your website by browsing to **`<your domain>`/summit21/web/exercise/**. You should see the Technical Whitepaper render in a Full Window mode within your browser.

## Accessing Analytics APIs {#part3}

Now that you've successfully created a web page that has PDF Embed API rendering a PDF, you can now explore how to use JavaScript events to measure analytics to understand how users are using PDFs.

### Finding documentation

There are a lot of different JavaScript events available as part of PDF Embed API. You can access them from Adobe Document Services documentation.
1. Navigate to the [documentation](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html) site.
1. Review the different event types available as part of the API. These are helpful for reference and will also be helpful for your future projects.

   ![Screenshot of reference guide](assets/ControlPDF_17.png)

1. Copy the sample code listed on the website. Use this as the basis for our code and modify it.

   ![Screenshot of where to copy sample code](assets/ControlPDF_18.png)

   ```
   const eventOptions = {
     //Pass the PDF analytics events to receive.
      //If no event is passed in listenOn, then all PDF 		analytics events will be received.
   listenOn: [ AdobeDC.View.Enum.PDFAnalyticsEvents.	PAGE_VIEW, AdobeDC.View.Enum.PDFAnalyticsEvents.DOCUMENT_DOWNLOAD],
     enablePDFAnalytics: true
   }


   adobeDCView.registerCallback(
     AdobeDC.View.Enum.CallbackType.EVENT_LISTENER,
     function(event) {
       console.log(“Type “ + event.type);
       console.log(“Data “ + event.data);
     }, eventOptions
   );
   ```

1. In **index.html**, find the code section you added earlier that looks like the below and append the code above after this code:

   ![Screenshot of where to paste code](assets/ControlPDF_19.png)

In your web browser, load the page and open the Console to view the console outputs from the different events as you interact with the PDF viewer. 

   ![Screenshot of loading the page](assets/ControlPDF_20.png)

   ![Screenshot of the code for loading the page](assets/ControlPDF_21.png)

### Add Switch for capturing events

Now that you have the events being outputted to console.log, let's change the behavior based on which events. To do this, you'll use a switch example.
1. In the tutorial code, navigate to **snippets/eventsSwitch.js** and copy the contents of the file.

   ![Screenshot of where to copy code](assets/ControlPDF_22.png)

1. In the event listener function, paste the code in.

   ![Screenshot of where to paste code](assets/ControlPDF_23.png)

1. In the web browser, see that the console outputs correctly when the page is loaded and the PDF Viewer is interacted with.

### Adobe Analytics

If you want to add Adobe Analytics support to your viewer, you can follow the instructions documented on the website. 

>[!IMPORTANT]
>
>Your webpage needs to already have Adobe Analytics loaded on the page in the header.

Navigate to the Adobe Analytics documentation (https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtodata.html#adobe-analytics) and review if you already have Adobe Analytics enabled on your webpage. Follow instructions to set up a reportSuite.

### Google Analytics

![Screenshot of how to integrate with Google Analytics](assets/ControlPDF_24.png)

Adobe PDF Embed API provides out-of-box integration with Adobe Analytics. However, because all events are available as JavaScript events, it is possible to integrate with Google Analytics by capturing PDF events and using the ga() function to add the event to Adobe Analytics.
1. To see how you can integrate with Google Analytics, navigate to **snippets/eventsSwitchGA.js**.
1. Review and use this code as an example if your webpage is tracked using Adobe Analytics and it is already embedded on the webpage.

   ![Screenshot of Adobe Analytics code](assets/ControlPDF_25.png)

In part 4, you'll walk through how to layer on top of your PDF viewer a paywall that will show after you scroll past the second page.

## Add interactivity based on events {#part4}

### Paywall Example

Navigate to https://www3.technologyevaluation.com/research/white-paper/the-forrester-wave-digital-decisioning-platforms-q4-2020.html to see an example of a PDF behind a paywall. In this tutorial, you'll learn to add interactivity on top of a PDF viewing experience.

### Add Paywall Code

1. Go to snippets/paywallCode.html and copy the contents.
1. In exercise/index.html search for `<!-- TODO: EXERCISE 3: INSERT PAYWALL CODE -->`.

   ![Screenshot of where to copy code](assets/ControlPDF_26.png)

1. Paste the copied code after the comment.
1. Go to **snippets/paywallCode.js** and copy the contents.

   ![Screenshot of where to paste code](assets/ControlPDF_27.png)

1. Paste the code into that location.

### Try Demo with Paywall

Now you can view the demo.
1. Reload **index.html** on your website.
1. Scroll down to a page > 2.
1. Show the dialog come up to challenge user after the second page.

   ![Screenshot of viewing the demo](assets/ControlPDF_28.png)

## Additional resources

For additional resources, go to https://www.adobe.io/apis/documentcloud/dcsdk/docs.html.
