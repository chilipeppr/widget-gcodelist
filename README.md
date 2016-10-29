# com-chilipeppr-widget-gcode
The Gcode widget shows you the Gcode loaded into your workspace, lets you send it to the serial port, get back per line status, see an estimated length of time to execute the Gcode, navigate to the XYZ position of a specific line, and much more.

![alt text](screenshot.png "Screenshot")

## ChiliPeppr Widget / Gcode v5

All ChiliPeppr widgets/elements are defined using cpdefine() which is a method
that mimics require.js. Each defined object must have a unique ID so it does
not conflict with other ChiliPeppr widgets.

| Item                  | Value           |
| -------------         | ------------- | 
| ID                    | com-chilipeppr-widget-gcode |
| Name                  | Widget / Gcode v5 |
| Description           | The Gcode widget shows you the Gcode loaded into your workspace, lets you send it to the serial port, get back per line status, see an estimated length of time to execute the Gcode, navigate to the XYZ position of a specific line, and much more. |
| chilipeppr.load() URL | http://raw.githubusercontent.com/chilipeppr/widget-gcodelist/master/auto-generated-widget.html |
| Edit URL              | http://ide.c9.io/chilipeppr/widget-gcodelist |
| Github URL            | http://github.com/chilipeppr/widget-gcodelist |
| Test URL              | https://preview.c9users.io/chilipeppr/widget-gcodelist/widget.html |

## Example Code for chilipeppr.load() Statement

You can use the code below as a starting point for instantiating this widget 
inside a workspace or from another widget. The key is that you need to load 
your widget inlined into a div so the DOM can parse your HTML, CSS, and 
Javascript. Then you use cprequire() to find your widget's Javascript and get 
back the instance of it.

```javascript
// Inject new div to contain widget or use an existing div with an ID
$("body").append('<' + 'div id="myDivWidgetGcode"><' + '/div>');

chilipeppr.load(
  "#myDivWidgetGcode",
  "http://raw.githubusercontent.com/chilipeppr/widget-gcodelist/master/auto-generated-widget.html",
  function() {
    // Callback after widget loaded into #myDivWidgetGcode
    // Now use require.js to get reference to instantiated widget
    cprequire(
      ["inline:com-chilipeppr-widget-gcode"], // the id you gave your widget
      function(myObjWidgetGcode) {
        // Callback that is passed reference to the newly loaded widget
        console.log("Widget / Gcode v5 just got loaded.", myObjWidgetGcode);
        myObjWidgetGcode.init();
      }
    );
  }
);

```

## Publish

This widget/element publishes the following signals. These signals are owned by this widget/element and are published to all objects inside the ChiliPeppr environment that listen to them via the 
chilipeppr.subscribe(signal, callback) method. 
To better understand how ChiliPeppr's subscribe() method works see amplify.js's documentation at http://amplifyjs.com/api/pubsub/

  <table id="com-chilipeppr-elem-pubsubviewer-pub" class="table table-bordered table-striped">
      <thead>
          <tr>
              <th style="">Signal</th>
              <th style="">Description</th>
          </tr>
      </thead>
      <tbody>
      <tr valign="top"><td>/com-chilipeppr-widget-gcode/onplay</td><td>When user hits play button</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/onpause</td><td>When user hits pause button. The payload is a true/false boolean indicating whether it is a pause or an unpause. This event also fires if a pause is triggered for a different reason like from an M6 command. In that case the 2nd paramater of the payload contains a string of "m6".</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/onstop</td><td>When user hits stop button</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/resize</td><td>When we resize in case any other widget wants to listen to that so it can resize itself.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/done</td><td>When we are done sending all gcode. We send a payload of how many lines sent and time for job.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/recvGcode</td><td>We publish this signal after you send in a /requestGcode signal. We will send you the Gcode in this widget including metadata. The payload contains { lines: [(your lines as a 0-based array)], metadata: [{isSent: bool, isQueued: bool, isWritten: bool, isComplete: bool}] }. The lines are a 0-based array so if you are trying to line them up with the numbers in the Gcode widget, line 1 corresponds to lines[0] in the array. The metadata is an array that matches the lines array but contains data on the send state of each line. You are being sent an exact reference to that array so if you modify it, you are modifying the actual Gcode and Metadata in this widget. You have been warned.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/onChiliPepprPause</td><td>This event is published during the play operation when the chilipeppr_pause string is found inside the Gcode. You can place a chilipeppr_pause string anywhere in the Gcode inside a comment and it will tell ChiliPeppr to pause sending when it hits that line. <br><br>The pause acts much like the M6 tool change pause but it is a pause just for ChiliPeppr to interpret and not the CNC controller. This technique was implemented for synchronizing a secondary microcontroller that could trigger an out-of-band event like a laser solderer, or a solder paste dispenser, or some other control that had to trigger at a specific Gcode line. This specific event is not very helpful to subscribe to because it does not have as much meaning as onChiliPepprPauseOnComplete or onChiliPepprPauseOnExecute. Those two events are triggered when the CNC controller actually gets to the specifically paused line and that is where you would synchronize from. <br><br>The onChiliPepprPauseOnComplete is triggered by all CNC controllers but is not a very accurate event as most controllers have a planner buffer which means this line is not really what is being executed but will be executed in the very near future at an unknown time. The onChiliPepprPauseOnExecute is a highly accurate event currently only available from TinyG that tells you the CNC controller is exactly executing this line at this moment.<br><br>A sample macro is available that shows how to use the onChiliPepprPause events.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/onChiliPepprPauseOnExecute</td><td>This event is published when the line that contained the chilipeppr_pause command inside a comment has actually executed in the CNC controller. This is currently supported by TinyG. Grbl does not support this.<br><br>The payload of the event includes the line number and the line of gcode that was just executed like { line: 8, gcode: "(chilipeppr_pause solder drop 4)" }</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/onChiliPepprPauseOnComplete</td><td>This event is published when the line that contained the chilipeppr_pause command inside a comment has been actually sent to the CNC controller. If your controller supports onExecute events then it is recommended you use onChiliPepprPauseOnExecute instead because it is much more accurate.<br><br>To help explain the difference between onChiliPepprPauseOnExecute and onChiliPepprPauseOnComplete, for the onChiliPepprPauseOnComplete event if 1000 lines were buffered to SPJS and this is the 1000th line that this event will only trigger as the 1000th line is dished up to the CNC controller. This gives an event that can be pivoted off of. If the CNC controller has a planner buffer that can hold around 8 moves, you will get this event only when this line of gcode enters the planner buffer. So you are getting the event 8 moves ahead. To make this work in the real world you should likely pause by a length of time to ensure all commands are done or issue a double check command to your CNC controller that sends you back a response where you can ensure the controller is in the correct state.<br><br>The payload of the event includes the line number and the line of gcode that was just completed like { line: 8, gcode: "(chilipeppr_pause solder drop 4)" }</td></tr>    
      </tbody>
  </table>

## Subscribe

This widget/element subscribes to the following signals. These signals are owned by this widget/element. Other objects inside the ChiliPeppr environment can publish to these signals via the chilipeppr.publish(signal, data) method. 
To better understand how ChiliPeppr's publish() method works see amplify.js's documentation at http://amplifyjs.com/api/pubsub/

  <table id="com-chilipeppr-elem-pubsubviewer-sub" class="table table-bordered table-striped">
      <thead>
          <tr>
              <th style="">Signal</th>
              <th style="">Description</th>
          </tr>
      </thead>
      <tbody>
      <tr valign="top"><td>/com-chilipeppr-widget-gcode/requestGcode</td><td>Send in this signal and we will publish back out a /recvGcode signal. See /recvGcode for further info.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/stop</td><td>Send in this signal to stop the gcode playing. Equivalent to hitting the stop button.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/play</td><td>Send in this signal to play the gcode. Equivalent to hitting the play button.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/pause</td><td>Send in this signal to pause/unpause the gcode. Equivalent to hitting the pause button.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/jumpToLine</td><td>Send in an integer of what line to jump to in the Gcode widget. The index you send it should match what visually shows in the Gcode widget, i.e. line 1 should be sent as a 1 (even though the backing array is index 0).</td></tr>    
      </tbody>
  </table>

## Foreign Publish

This widget/element publishes to the following signals that are owned by other objects. 
To better understand how ChiliPeppr's subscribe() method works see amplify.js's documentation at http://amplifyjs.com/api/pubsub/

  <table id="com-chilipeppr-elem-pubsubviewer-foreignpub" class="table table-bordered table-striped">
      <thead>
          <tr>
              <th style="">Signal</th>
              <th style="">Description</th>
          </tr>
      </thead>
      <tbody>
      <tr valign="top"><td>/com-chilipeppr-widget-gcode/com-chilipeppr-widget-3dviewer/gcodeline</td><td>When a user clicks a line of gcode. We'll send the gcode and the line number so the subscriber can sync to what we're sending, i.e. a 3D viewer that wants to hilite the line representing this command. The format of the signal data is something like {line: 12, gcode: "G1 F300.0 Z0.0"}.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/com-chilipeppr-elem-flashmsg/flashmsg</td><td>Send a flash message on certain events. In particular, we send a flash message when a comment line is hit. We also send a flash message when the gcode is done sending.</td></tr>    
      </tbody>
  </table>

## Foreign Subscribe

This widget/element publishes to the following signals that are owned by other objects.
To better understand how ChiliPeppr's publish() method works see amplify.js's documentation at http://amplifyjs.com/api/pubsub/

  <table id="com-chilipeppr-elem-pubsubviewer-foreignsub" class="table table-bordered table-striped">
      <thead>
          <tr>
              <th style="">Signal</th>
              <th style="">Description</th>
          </tr>
      </thead>
      <tbody>
      <tr valign="top"><td>/com-chilipeppr-widget-gcode/com-chilipeppr-elem-dragdrop/ondropped</td><td>We watch for a drag drop event of the Gcode file. The payload of the signal contains the localStorage object which is just the string representing the entire Gcode file.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/com-chilipeppr-interface-cnccontroller/plannerpause</td><td>We need to pause when planner tells us.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/com-chilipeppr-interface-cnccontroller/plannerresume</td><td>We need to resume when planner tells us.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/com-chilipeppr-interface-cnccontroller/feedhold</td><td>We need to place a manual pause if we see the e-stop hit. That way user can start where they left off.</td></tr><tr valign="top"><td>/com-chilipeppr-widget-gcode/com-chilipeppr-interface-cnccontroller/onExecute</td><td>This signal is sent to us by the CNC controller widget. The payload looks like { line: 234 }. It is ONLY sent if the CNC controller widget is able to determine an executed state. Please refer to your CNC controller's publish documenation to determine if this signal will be published and thus whether this Gcode widget will see these events.</td></tr>    
      </tbody>
  </table>

## Methods / Properties

The table below shows, in order, the methods and properties inside the widget/element.

  <table id="com-chilipeppr-elem-methodsprops" class="table table-bordered table-striped">
      <thead>
          <tr>
              <th style="">Method / Property</th>
              <th>Type</th>
              <th style="">Description</th>
          </tr>
      </thead>
      <tbody>
      <tr valign="top"><td>id</td><td>string</td><td>"com-chilipeppr-widget-gcode"</td></tr><tr valign="top"><td>url</td><td>string</td><td>"http://raw.githubusercontent.com/chilipeppr/widget-gcodelist/master/auto-generated-widget.html"</td></tr><tr valign="top"><td>fiddleurl</td><td>string</td><td>"http://ide.c9.io/chilipeppr/widget-gcodelist"</td></tr><tr valign="top"><td>githuburl</td><td>string</td><td>"http://github.com/chilipeppr/widget-gcodelist"</td></tr><tr valign="top"><td>testurl</td><td>string</td><td>"http://widget-gcodelist-chilipeppr.c9users.io/widget.html"</td></tr><tr valign="top"><td>name</td><td>string</td><td>"Widget / Gcode v5"</td></tr><tr valign="top"><td>desc</td><td>string</td><td>"The Gcode widget shows you the Gcode loaded into your workspace, lets you send it to the serial port, get back per line status, see an estimated length of time to execute the Gcode, navigate to the XYZ position of a specific line, and much more."</td></tr><tr valign="top"><td>publish</td><td>object</td><td>Please see docs above.</td></tr><tr valign="top"><td>subscribe</td><td>object</td><td>Please see docs above.</td></tr><tr valign="top"><td>foreignSubscribe</td><td>object</td><td>Please see docs above.</td></tr><tr valign="top"><td>foreignPublish</td><td>object</td><td>Please see docs above.</td></tr><tr valign="top"><td>fileLines</td><td>object</td><td></td></tr><tr valign="top"><td>metaLines</td><td>object</td><td></td></tr><tr valign="top"><td>metaObj</td><td>object</td><td></td></tr><tr valign="top"><td>linesComplete</td><td>object</td><td></td></tr><tr valign="top"><td>linesToShow</td><td>number</td><td></td></tr><tr valign="top"><td>linesToShowMax</td><td>number</td><td></td></tr><tr valign="top"><td>delayPerLine</td><td>number</td><td></td></tr><tr valign="top"><td>lineNumbersOnByDefault</td><td>boolean</td><td></td></tr><tr valign="top"><td>isDirtyUnits</td><td>boolean</td><td></td></tr><tr valign="top"><td>init</td><td>function</td><td>function (settings) </td></tr><tr valign="top"><td>setupJumpToLine</td><td>function</td><td>function () </td></tr><tr valign="top"><td>onJumpToLine</td><td>function</td><td>function (evt) </td></tr><tr valign="top"><td>getEstimates</td><td>function</td><td>function () </td></tr><tr valign="top"><td>setupStatusSteps</td><td>function</td><td>function () </td></tr><tr valign="top"><td>setupPlayPauseStopPubSub</td><td>function</td><td>function () </td></tr><tr valign="top"><td>setupSendGcodePubSub</td><td>function</td><td>function () </td></tr><tr valign="top"><td>publishGcode</td><td>function</td><td>function () </td></tr><tr valign="top"><td>options</td><td>object</td><td></td></tr><tr valign="top"><td>setupOptionsModal</td><td>function</td><td>function () </td></tr><tr valign="top"><td>saveOptionsModal</td><td>function</td><td>function () </td></tr><tr valign="top"><td>showOptionsModal</td><td>function</td><td>function () </td></tr><tr valign="top"><td>hideOptionsModal</td><td>function</td><td>function () </td></tr><tr valign="top"><td>setupToolChangeModal</td><td>function</td><td>function () </td></tr><tr valign="top"><td>isInToolChangeMode</td><td>boolean</td><td></td></tr><tr valign="top"><td>toolNumber</td><td>object</td><td></td></tr><tr valign="top"><td>toolChangeRepositionCmd</td><td>object</td><td></td></tr><tr valign="top"><td>toolChangeCmd</td><td>object</td><td></td></tr><tr valign="top"><td>showToolChangeModal</td><td>function</td><td>function () </td></tr><tr valign="top"><td>hideToolChangeModal</td><td>function</td><td>function () </td></tr><tr valign="top"><td>hideToolChangeDiv</td><td>function</td><td>function (wasPaused) </td></tr><tr valign="top"><td>showUOMModal</td><td>function</td><td>function (txt, info, skipLocalStore)</td></tr><tr valign="top"><td>hideUOMModal</td><td>function</td><td>function ()</td></tr><tr valign="top"><td>setUOM</td><td>function</td><td>function (txt, info, skipLocalStore, units)</td></tr><tr valign="top"><td>getCoordFromControllerRecvCallback</td><td>object</td><td></td></tr><tr valign="top"><td>getCoordFromController</td><td>function</td><td>function (callback) </td></tr><tr valign="top"><td>getCoordFromControllerRecv</td><td>function</td><td>function (coords) </td></tr><tr valign="top"><td>getMotorConfigCallback</td><td>function</td><td>function (data) </td></tr><tr valign="top"><td>getXyzCoordsForLineRecv3dObjCallback</td><td>object</td><td></td></tr><tr valign="top"><td>getXyzCoordsForLineRecv3dObjLine</td><td>object</td><td></td></tr><tr valign="top"><td>obj3d</td><td>object</td><td></td></tr><tr valign="top"><td>getXyzCoordsForLine</td><td>function</td><td>function (line, callback) </td></tr><tr valign="top"><td>getXyzCoordsForLineRecv3dObj</td><td>function</td><td>function (obj3d) </td></tr><tr valign="top"><td>btnSetup</td><td>function</td><td>function () </td></tr><tr valign="top"><td>onFeedhold</td><td>function</td><td>function () </td></tr><tr valign="top"><td>pauseBtnIcon</td><td>object</td><td></td></tr><tr valign="top"><td>onPlannerPause</td><td>function</td><td>function () </td></tr><tr valign="top"><td>onPlannerResume</td><td>function</td><td>function () </td></tr><tr valign="top"><td>isBodyShowing</td><td>boolean</td><td></td></tr><tr valign="top"><td>showBody</td><td>function</td><td>function (evt) </td></tr><tr valign="top"><td>hideBody</td><td>function</td><td>function (evt) </td></tr><tr valign="top"><td>forkSetup</td><td>function</td><td>function () </td></tr><tr valign="top"><td>setupResizeable</td><td>function</td><td>function () </td></tr><tr valign="top"><td>setupFeedrateAdjust</td><td>function</td><td>function () </td></tr><tr valign="top"><td>feedrateDisable</td><td>function</td><td>function () </td></tr><tr valign="top"><td>feedrateEnable</td><td>function</td><td>function () </td></tr><tr valign="top"><td>feedrateUp</td><td>function</td><td>function () </td></tr><tr valign="top"><td>feedrateDown</td><td>function</td><td>function () </td></tr><tr valign="top"><td>feedrateReset</td><td>function</td><td>function () </td></tr><tr valign="top"><td>feedrateAdjust</td><td>function</td><td>function () </td></tr><tr valign="top"><td>feedrateUpdateDom</td><td>function</td><td>function () </td></tr><tr valign="top"><td>onRecv3dObjectPerRowTd</td><td>object</td><td></td></tr><tr valign="top"><td>isShowingCoords</td><td>boolean</td><td></td></tr><tr valign="top"><td>isInsideCoords</td><td>boolean</td><td></td></tr><tr valign="top"><td>onRecv3dObjectPerRow</td><td>function</td><td>function (obj3d) </td></tr><tr valign="top"><td>setNewStartPosition</td><td>function</td><td>function (indx, tr) </td></tr><tr valign="top"><td>showXyzCoordsForRow</td><td>function</td><td>function (td) </td></tr><tr valign="top"><td>setupRowTrigger</td><td>function</td><td>function () </td></tr><tr valign="top"><td>timeStart</td><td>object</td><td></td></tr><tr valign="top"><td>timeEnd</td><td>object</td><td></td></tr><tr valign="top"><td>isPlayStep</td><td>boolean</td><td></td></tr><tr valign="top"><td>isPlaying</td><td>boolean</td><td></td></tr><tr valign="top"><td>isPaused</td><td>boolean</td><td></td></tr><tr valign="top"><td>isPausedByPlanner</td><td>boolean</td><td></td></tr><tr valign="top"><td>preUploadRemainder</td><td>number</td><td></td></tr><tr valign="top"><td>currentLine</td><td>object</td><td></td></tr><tr valign="top"><td>onPauseByPlanner</td><td>function</td><td>function (event) </td></tr><tr valign="top"><td>onStepBack</td><td>function</td><td>function (evt) </td></tr><tr valign="top"><td>onStepFwd</td><td>function</td><td>function (evt) </td></tr><tr valign="top"><td>onPause</td><td>function</td><td>function (event, isFromM6, isFromCpPause) </td></tr><tr valign="top"><td>resetMetaDataQueueWriteComplete</td><td>function</td><td>function (indexToStartAfter) </td></tr><tr valign="top"><td>onStop</td><td>function</td><td>function (event) </td></tr><tr valign="top"><td>isResetMetaBeforePlay</td><td>boolean</td><td></td></tr><tr valign="top"><td>isJobDonePubSubSent</td><td>boolean</td><td></td></tr><tr valign="top"><td>onPlay</td><td>function</td><td>function (event) </td></tr><tr valign="top"><td>isInMultiLineMode</td><td>boolean</td><td></td></tr><tr valign="top"><td>multiLines</td><td>number</td><td></td></tr><tr valign="top"><td>onPlayMultiLine</td><td>function</td><td>function () </td></tr><tr valign="top"><td>onPreUpload</td><td>function</td><td>function (numToUpload) </td></tr><tr valign="top"><td>preUploadGang</td><td>string</td><td></td></tr><tr valign="top"><td>preUploadGangArr</td><td>object</td><td></td></tr><tr valign="top"><td>onPreUploadCallback</td><td>function</td><td>function (data) </td></tr><tr valign="top"><td>statEls</td><td>object</td><td></td></tr><tr valign="top"><td>isPlayNextLineNoScroll</td><td>boolean</td><td></td></tr><tr valign="top"><td>onPlayNextLine</td><td>function</td><td>function () </td></tr><tr valign="top"><td>onChiliPepprPause</td><td>function</td><td>function (meta) </td></tr><tr valign="top"><td>onChiliPepprPauseOnComplete</td><td>function</td><td>function (meta) </td></tr><tr valign="top"><td>onChiliPepprPauseOnExecute</td><td>function</td><td>function (meta) </td></tr><tr valign="top"><td>toHHMMSS</td><td>function</td><td>function (secs) </td></tr><tr valign="top"><td>gotoLine</td><td>function</td><td>function (linenum, isMarkSent) </td></tr><tr valign="top"><td>fileInfo</td><td>object</td><td></td></tr><tr valign="top"><td>onFileLoaded</td><td>function</td><td>function (txt, info, skipLocalStore) </td></tr><tr valign="top"><td>resendGcodeToWorkspace</td><td>function</td><td>function () </td></tr><tr valign="top"><td>jumpToTop</td><td>function</td><td>function () </td></tr><tr valign="top"><td>jumpToLine</td><td>function</td><td>function (linenum) </td></tr><tr valign="top"><td>setupJumpScroll</td><td>function</td><td>function () </td></tr><tr valign="top"><td>preSetupInfiniteScroll</td><td>function</td><td>function (doTopScrollOffset) </td></tr><tr valign="top"><td>infiniteScrollDestroy</td><td>function</td><td>function () </td></tr><tr valign="top"><td>setupInfiniteScroll</td><td>function</td><td>function (doTopScrollOffset) </td></tr><tr valign="top"><td>showingStartIndex</td><td>number</td><td></td></tr><tr valign="top"><td>showingEndIndex</td><td>number</td><td></td></tr><tr valign="top"><td>showingStats</td><td>function</td><td>function () </td></tr><tr valign="top"><td>setupOnQueueWriteCompletePubSub</td><td>function</td><td>function () </td></tr><tr valign="top"><td>jsonOnSend</td><td>function</td><td>function (data) </td></tr><tr valign="top"><td>onQueue</td><td>function</td><td>function (data) </td></tr><tr valign="top"><td>onWrite</td><td>function</td><td>function (data) </td></tr><tr valign="top"><td>onComplete</td><td>function</td><td>function (data) </td></tr><tr valign="top"><td>onError</td><td>function</td><td>function (data) </td></tr><tr valign="top"><td>isInExecuteScrollToMode</td><td>boolean</td><td></td></tr><tr valign="top"><td>lastLineMarkedExecuted</td><td>object</td><td></td></tr><tr valign="top"><td>onExecute</td><td>function</td><td>function (data) </td></tr><tr valign="top"><td>updateRowQueueStats</td><td>function</td><td>function (linenum) </td></tr><tr valign="top"><td>updateRowQueueStatsEl</td><td>function</td><td>function (rowEl) </td></tr><tr valign="top"><td>prependRows</td><td>function</td><td>function (endIndx) </td></tr><tr valign="top"><td>appendRows</td><td>function</td><td>function (startIndx, skipWaypoint) </td></tr><tr valign="top"><td>removeAllRows</td><td>function</td><td>function () </td></tr><tr valign="top"><td>removeRowsFromStart</td><td>function</td><td>function () </td></tr><tr valign="top"><td>removeRowsFromEnd</td><td>function</td><td>function () </td></tr><tr valign="top"><td>feedrateEl</td><td>object</td><td></td></tr><tr valign="top"><td>getFeedrateMarkup</td><td>function</td><td>function (html, isJustGettingRaw) </td></tr><tr valign="top"><td>getCommentMarkup</td><td>function</td><td>function (html) </td></tr><tr valign="top"><td>getTooltipMarkup</td><td>function</td><td>function (txt) </td></tr><tr valign="top"><td>lastElemIndex</td><td>object</td><td></td></tr><tr valign="top"><td>cleanupHilites</td><td>function</td><td>function () </td></tr><tr valign="top"><td>cleanupDomElem</td><td>function</td><td>function (elem) </td></tr><tr valign="top"><td>parseGcodeForDomElem</td><td>function</td><td>function (elem, doTooltip) </td></tr><tr valign="top"><td>parseGcode</td><td>function</td><td>function () </td></tr><tr valign="top"><td>createGcodeDesc</td><td>function</td><td>function () </td></tr><tr valign="top"><td>gcodeIsLoaded</td><td>boolean</td><td></td></tr><tr valign="top"><td>gcodeData</td><td>object</td><td></td></tr><tr valign="top"><td>gcodeLoad</td><td>function</td><td>function (callback, context) </td></tr><tr valign="top"><td>obj3dmeta</td><td>object</td><td></td></tr><tr valign="top"><td>userCallbackForGet3dObj</td><td>object</td><td></td></tr><tr valign="top"><td>get3dObj</td><td>function</td><td>function (callback) </td></tr><tr valign="top"><td>get3dObjCallback</td><td>function</td><td>function (data, meta) </td></tr><tr valign="top"><td>calcEstimatedRemain</td><td>function</td><td>function (indexOfLineAt) </td></tr>
      </tbody>
  </table>


## About ChiliPeppr

[ChiliPeppr](http://chilipeppr.com) is a hardware fiddle, meaning it is a 
website that lets you easily
create a workspace to fiddle with your hardware from software. ChiliPeppr provides
a [Serial Port JSON Server](https://github.com/johnlauer/serial-port-json-server) 
that you run locally on your computer, or remotely on another computer, to connect to 
the serial port of your hardware like an Arduino or other microcontroller.

You then create a workspace at ChiliPeppr.com that connects to your hardware 
by starting from scratch or forking somebody else's
workspace that is close to what you are after. Then you write widgets in
Javascript that interact with your hardware by forking the base template 
widget or forking another widget that
is similar to what you are trying to build.

ChiliPeppr is massively capable such that the workspaces for 
[TinyG](http://chilipeppr.com/tinyg) and [Grbl](http://chilipeppr.com/grbl) CNC 
controllers have become full-fledged CNC machine management software used by
tens of thousands.

ChiliPeppr has inspired many people in the hardware/software world to use the
browser and Javascript as the foundation for interacting with hardware. The
Arduino team in Italy caught wind of ChiliPeppr and now
ChiliPeppr's Serial Port JSON Server is the basis for the 
[Arduino's new web IDE](https://create.arduino.cc/). If the Arduino team is excited about building on top
of ChiliPeppr, what
will you build on top of it?

