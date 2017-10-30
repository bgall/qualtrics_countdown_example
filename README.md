# Example Cross-Page Countdown Timer with Auto-Advance

Although software abounds for the implementation of online, timed behavioral studies, many researchers combining behavioral experiments with survey data collection might wish to use the same platform for the behavioral task as for survey data collection. This example is intended to reduce the barriers to doing so by providing an example of the implementation of a feature that is necessary for many online behavioral experiments but which can be somewhat burdensome to implement in existing software.

Specifically, this example explains how to (a) implement a timer that persists across screens (and/or blocks) of a study while (b) auto-advancing the participant to a post-task screen (c) in Qualtrics. Although these features are trivial to implement in some software specifically designed for behavioral experiments, many researchers find Qualtric offers attractive features that make it desirable for their survey data collection and so end up using multiple platforms.

The example is applicable in nearly all uses cases: any situations where a single question or multiple questions are contained in a single page or multiple pages within a single question block or multiple question blocks. Although here we will assume that the task can be represented in a survey-based response (as is typically the case in choice-based tasks in the social sciences), presumably this can be extended to other cases.

This example was informed by the work of [Andrew Long](https://thebehaviorallab.wordpress.com/2016/05/13/custom-timers-and-times-up-notifications-in-qualtrics/) and [T. Gibbons](https://stackoverflow.com/users/4434072/t-gibbons), in particular [this post](https://stackoverflow.com/questions/45703003/qualtrics-progress-to-next-block-when-time-is-up).

## The Demonstration-Case

For demonstration purposes, we'll focus on a concrete example. In this example, study participants have a limited amount of time (5 minutes) to decode as many codes as they can. After clicking a button to begin the experiment, participants will complete an experiment/task across multiple screens. 

On each screen, they are provided with some codes and table that provide the crosswalk between coded values and decoded values. They can enter the decoded values on the same screen and can move forwards or backwards between screens throughout the time limit. At the end of the time limit, they will be forwarded to a post-experiment screen. We will assume that each screen is a different block of values/crosswalks/answer spaces - although this is not necessary - and that the researcher does not want the participant to advance beyond last page of questions prior to the end of the time window.

Once participants click through to the start of the experiments, we want to ensure that the participants can track the amount of time remaining on the task. In such cases, providing a clear countdown timer at the top of the screen is useful. 

# Setting up your survey

You'll first want to create all of your survey questions. In this exampe, we're assuming that we have many blocks with a single page containing whatever number of questions of whatever type we want. 

# Adding a counterdown timer

Although Qualtrics offers a countdown timer, this timer can only track time spent on a single page. This is problematic when a study requires the participant to view multiple pages and possibly even multiple blocks of pages. 

We can add a countdown timer that will persist across multiple pages by using some JavaScript code. First, we should add a 'Timing' type question (see Qualtrics' [timing section](https://www.qualtrics.com/support/survey-platform/survey-module/editing-questions/question-types-guide/advanced/timing/) for how to add these) to the block or page the participant will first see after clicking through to begin the experiment. The benefit to using a Timing question type is that the participant never sees the question and including the JavaScript here ensures it is isolted from any other JavaScript you will use.

To add JavaScript, click question, click the gear symbol ("Advanced Question Options") on the left-hand side of the question, then click "Add Javascript..."  This should open an embedded window. You'll want to clear anything in that window and then copy and paste the below into this window and click Save:

```js
Qualtrics.SurveyEngine.addOnload(function()
{
    var headerCont = document.createElement("div");  
 headerCont.className = "header-cont";  
 headerCont.id = "header_container";  
 var header = document.createElement("div");  
 header.className = "header"  
 header.id = "header_1";  
 var timer = document.createElement("div");  
 timer.className = "timer";  
 timer.id = "timer_1";  
 timer.innerHTML = "Time Remaining: <span id='time'>00:10</span>";  
 headerCont.appendChild(header);  
 header.appendChild(timer);  
 document.body.insertBefore(headerCont, document.body.firstChild);
function startTimer(duration, display) {  
  var timer = duration, minutes, seconds;  
  var myTimer = setInterval(function() {  
   minutes = parseInt(timer / 60, 10)  
   seconds = parseInt(timer % 60, 10);  
   minutes = minutes < 10 ? "0" + minutes : minutes;  
   seconds = seconds < 10 ? "0" + seconds : seconds;  
   var text = ('innerText' in display)? 'innerText' : 'textContent';
   display[text] = minutes + ":" + seconds;  
   if (--timer < 0) {  
    clearInterval(myTimer);  
    timeOver();  
   }  
  }, 1000);  
 }  
	
 var timerSeconds = 5,  
 display = document.querySelector('#time');  
 startTimer(timerSeconds, display);  
 var timeOver = function() {  
	 
     document.getElementById("timer_1").innerHTML = "";
     Qualtrics.SurveyEngine.setEmbeddedData("blockTimeFlag", "1");    
     $('NextButton').click();}

});

```

Although there is a lot going on in this script, the most important place for you to look is at the bottom. This effectively determines what will happen when time runs out on the countdown and is where you'll find the following:

- `var timerSeconds = 5`. This indicates that the timer should countdown from 5 seconds until 0 seconds. You can change the number of seconds to your designed number here. The script will convert your number of seconds to a more reasonable countdown in terms of minutes and seconds.

- `document.getElementById("timer_1").innerHTML = ""`. This allows you to place any text you would like at the top of the screen once time has elapsed. You can include the message (e.g. "Your time has run out!") in between the quotation marks. For example: `.innerHTML = "Your time has run out!"`. Note that this message will persist at the top of the study unless turned off or set as blank. Here, I've set the field to be empty - meaning that no message is shown once time has elapsed.

- `Qualtrics.SurveyEngine.setEmbeddedData("blockTimeFlag", "1")`. This tells Qualtrics to assign the embedded data field (EDF) named `blockTimeFlag` a value of 1. More on this later.

- `$('NextButton').click();}`. This results in time running out leading to a click of the Next button. This is what will enable use to advance to the end of the task after time has elapsed.

Each of these features (with the exception of the first) can be removed from the above code - perhaps relevant if you don't want to show a message or don't want to advance the screen once time elapses.

Once this is done, an orange icon should appear on the left-hand side of the question, indicating it contains JavaScript:

![JS Added](/javascript_added.PNG?raw=true)

# Advancing to another section when time runs out

### Embedded Data 

In our example, we have hundreds of screens for participants to view during the timed experiment and do not expect the participants to advance through all of the screens within the time constraints. However, we don't want to require them to click through all of those screens. Given that we don't know the screen on which the participant will run out of time, we want to implement a solution that advances the participant to a section of the study outside of the timed experiment from any screen in the experiment.

One solution to this is to generate a variable at the very beginning of the study, condition the display of the experiment's screens on that variable having a specific value, and assign that variable the specified value when time runs out. 

More concretely, we can go into the Survey Flow of the study and click "Add a New Element Here" then "Embedded Data" to add an Embedded Data Field to the Survey Flow. In the left-hand box, enter a field name that you like: this will be the name of the variable that will determine whether your other questions will appear. You then need to assign this field a value by clicking 'Set Value' in that box and entering a value of 0. 

![Embedded Data](/embedded_trigger.PNG?raw=true)

You need to make sure the EDF is earlier in your Survey Flow than your experiment. Best practice suggests keeping this EDF at the top of your survey flow.

After saving your survey flow, you'll need to return to the Javascript you previously copied, pasted, and saved. Instead of `blockTimeflag`, you'll want to enter the name of the field you just created. Once this is done, save. You have now instructed Qualtrics to create a variable with a default value of 0 and change the value to 1 once time has elapsed.

### Display Logic

We can now use the fact that the value of 0 of the EDF you just created will change to 1 when time has elapsed to ensure that participants will only see the task's screens so long as that value has not changed. To do this, change the Display Logic for each possible screen in the study to the condition that the EDF field is still 0. To do this, click on the question, then select "Add Display Logic" from the options that appear on the right-hand side of the screen. Enter the name of your EDF use the operator "is equal to" and enter 0 in the value field. This will ensure that the question is only displayed when time has not elapsed.

![Display Logic](/display_logic.PNG?raw=true)

Recall that the JavaScript provided above will click the "Next" button once time has run out. If all of the experiment's questions have this display logic, then when the the "Next" button is clicked, the participant will skip all of the rest of the experiment and appear in a new section of the study as desired.

# What if a participant finishes early?

Once issue that can arise from the above procedure is that if a participant completes the experiment before time has elapsed, Qualtrics will *still* automatically click "Next" on the current screen - even though this might accidentally cause the participant to skip a screen after the experiment and not be able to provide responses for that screen.

Although there are many solutions, one solution is to *not allow the participant to move to the next section of the study until the specified time limit has elapsed*. The appropriateness of this solution will vary depending upon the context of your study.

To implement this, we want to remove the "Next" button from the last screen of the experiment. To do this, it is recommended you create a block/page after your experiment with a Text Description question explaining that the participant has completed the experiment and must wait until the time limit has elapsed. Importantly, this screen should be subject to the display logic found above so as to ensure participants only see this screen if they finish before the time elapses. One can then add the following JavaScript to the question to ensure that the "Next" button is hidden from the question:

``` js
Qualtrics.SurveyEngine.addOnload(function() {     
     function hideEl(element) {    
         element.hide();   
      }    
     var nb = $('NextButton');  
     hideEl.defer(nb);})
```
