Simple Ajax Uploader
============================

Javascript plugin for AJAX-style file uploading with progress support.

Live demo: http://www.lpology.com/code/ajaxuploader/

### Overview ###
Simple Ajax Uploader allows developers to easily add AJAX-style file upload functionality to web applications. It attempts to make the development process more efficient by abstracting away common tasks (like calculating upload progress percentage) while maintaining wide latitude for customization.

This project began life as a rewrite of Andrew Valum's original Ajax Upload plugin. Continuing the simplistic design and customizable nature of Andrew's original plugin is a top priority.

### Features ###
* Uses XMLHttpRequest and HTML5 file API with fall back to iframe method for Internet Explorer.
* Use any HTML element as the upload button.
* Provides individual callback functions for XHR-supported browsers and for browsers that do not support XHR uploads. This allows for much greater control over user experience.
* Requires no external libraries.
* Fast and lightweight - only about 3.2KB when minified and gzipped.
* Tested in IE7+, Firefox 4+, Safari 4+, and Chrome.

### Getting Started ###
Include `SimpleAjaxUploader.js` into your page:

```html
	<script type="text/javascript" src="SimpleAjaxUploader.js"></script>
```

Initialize the uploader when the DOM is ready. There are three required parameters:

```javascript
var uploader = new ss.SimpleUpload({
	button: 'upload-btn',
	url: '/PathTo/UploadHandler.php',
	name: 'uploadfile'
});
```

### Using Uploader.php ###

Basic example of how to handle uploads on the server using the included PHP class:

```php
<?php
require('Uploader.php');

$upload_dir = '/img_uploads/';
$valid_extensions = array('gif', 'png', 'jpeg', 'jpg');

$Upload = new FileUpload('uploadfile');
$result = $Upload->handleUpload($upload_dir, $valid_extensions);

if (!$result) {
	echo json_encode(array('success' => false, 'msg' => $Upload->getErrorMsg()));	
} else {
	echo json_encode(array('success' => true, 'file' => $Upload->getFileName()));
}
```

You can also save the uploaded file with a different name by setting the `newFileName` property:

```php
$Upload = new FileUpload('uploadfile');
$ext = $Upload->getExtension(); // Get the extension of the uploaded file
$Upload->newFileName = 'customFileName.'.$ext;
$result = $Upload->handleUpload($upload_dir, $valid_extensions);
```

To access the newly uploaded file, use the `getSavedFile()` method to get the file's path after the upload is completed:
```php
$Upload = new FileUpload('uploadfile');
$result = $Upload->handleUpload($upload_dir, $valid_extensions);

if ($result) {
  $path = $Upload->getSavedFile();
  $imgsize = getimagesize($path);
  // image resizing stuff...
}
```


### Settings for SimpleAjaxUploader.js ###
<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><strong>button</strong><br />Default: <code>''</code></td>
            <td>String, Element</td>
            <td>File upload button. <strong>Required.</strong></td>
        </tr>
        <tr>
            <td><strong>url</strong><br />Default: <code>''</code></td>
            <td>String</td>
            <td>Location of the server-side file upload handler. <strong>Required.</strong></td>
        </tr>		
        <tr>
            <td><strong>name</strong><br />Default: <code>''</code></td>
            <td>String</td>
            <td>Upload parameter name. <strong>Required.</strong></td>
        </tr>
        <tr>
            <td><strong>data</strong><br />Default: <code>{}</code></td>
            <td>Object</td>
            <td>Additional data to be sent to the server.</td>
        </tr>
        <tr>
            <td><strong>autoSubmit</strong><br />Default: <code>true</code></td>
            <td>Boolean</td>
            <td>By default, uploads commence as soon as a file is selected. Set to false to delay the upload and trigger manually.</td>
        </tr>
        <tr>
            <td><strong>responseType</strong><br />Default: <code>'text'</code></td>
            <td>String</td>
            <td>The type of data you're expecting back from the server. Default is plain text. Additional option is 'json'.</td>
        </tr>		
        <tr>
            <td><strong>debug</strong><br />Default: <code>false</code></td>
            <td>Boolean</td>
            <td>Setting debug to true will log progress messages and server response in the console.</td>
        </tr>		
        <tr>
            <td><strong>hoverClass</strong><br />Default: <code>''</code></td>
            <td>String</td>
            <td>Class applied to upload button when mouse is hovered.</td>
        </tr>		
        <tr>
            <td><strong>focusClass</strong><br />Default: <code>''</code></td>
            <td>String</td>
            <td>Class applied to upload button when focused.</td>
        </tr>	
        <tr>
            <td><strong>disabledClass</strong><br />Default: <code>''</code></td>
            <td>String</td>
            <td>Class applied to button when disabled.</td>
        </tr>		
        <tr>
            <td><strong>onChange(filename, extension)</strong></td>
            <td>Function</td>
            <td>Function to be called when user selects a file. The function gets passed two arguments: a string containing the filename; a string containing the file extension.</td>
        </tr>
        <tr>
            <td><strong>onSubmit(filename, extension)</strong></td>
            <td>Function</td>
            <td>Function to be called before file is uploaded. The function gets passed two arguments: a string containing the filename; a string containing the file extension. Return false stops upload.</td>
        </tr>
        <tr>
            <td><strong>onProgress(pct)</strong></td>
            <td>Function</td>
            <td>Function to be called on the progress event for browsers that support XHR uploads. The function gets passed one argument: an integer representing the upload completion percentage.</td>
        </tr>
        <tr>
            <td><strong>onComplete(filename, response)</strong></td>
            <td>Function</td>
            <td>Function to be called when the upload is completed. The function gets passed two parameters: a string containing the filename; the data returned from the server, formatted according to the <code>responseType</code> setting. If <code>responseType</code> is 'json', the response will be evaluated as JSON and will return a Javascript object.</td>
        </tr>
        <tr>
            <td><strong>onError(filename, errorType, response)</strong></td>
            <td>Function</td>
            <td>Function to be called if an error occurs during upload. The function gets passsed three parameters: a string containing the filename; a string containing the error type; a string containing the server response, if any.</td>
        </tr>
        <tr>
            <td><strong>startXHR(filename, fileSize)</strong></td>
            <td>Function</td>
            <td>Function to be called only in browsers that support XHR uploads (non-IE). Executes after <code>onSubmit</code> but prior to upload start. The function gets passed two arguments: a string containing the filename; a number that is the file size in kilobytes. Return false stops upload.</td>
        </tr>
        <tr>
            <td><strong>endXHR(filename)</strong></td>
            <td>Function</td>
            <td>Function to be called only in browsers that support XHR uploads (non-IE). Executes after upload is completed but prior to <code>onComplete</code>. The function gets passed one argument: a string containing the filename.</td>
        </tr>
        <tr>
            <td><strong>startNonXHR(filename)</strong></td>
            <td>Function</td>
            <td>Function to be called only in browsers that do not support XHR uploads (Internet Explorer). Executes after <code>onSubmit</code> but prior to upload start. The function gets passed one argument: a string containing the filename. Return false stops upload.</td>
        </tr>
        <tr>
            <td><strong>endNonXHR(filename)</strong></td>
            <td>Function</td>
            <td>Function to be called only in browsers that do not support XHR uploads (Internet Explorer). Executes after upload is completed but prior to <code>onComplete</code>. The function gets passed one argument: a string containing the filename.</td>
        </tr>		
	</tbody>
</table>

### Instance Methods ###
<table>
    <thead>
        <tr>
            <th>Name</th>
			<th>Parameters</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><strong>submit()</strong></td>
			<td><i>none</i></td>
            <td>Begins the file upload process. Note that if <code>autoSubmit</code> is set to <code>true</code> (the default value), there is no need to manually call <code>submit()</code>. The upload process will begin immediately after the user selects a file.</td>
        </tr>	
        <tr>
            <td><strong>setData(data)</strong></td>
			<td><code>data</code> (Object)</td>
            <td>Replaces the data to be sent to the server. Note that all previously set data is entirely removed and replaced.</td>
        </tr>
        <tr>
            <td><strong>disable()</strong></td>
			<td><i>none</i></td>
            <td>Disables upload functionality.</td>
        </tr>		
        <tr>
            <td><strong>enable()</strong></td>
			<td><i>none</i></td>
            <td>Restores upload functionality.</td>
        </tr>			
	</tbody>
</table>

### License ###
Released under the MIT license.