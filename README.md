# Example Cross-Page Countdown Timer with Auto-Advance

Although software abounds for the implementation of online, timed behavioral studies, many researchers combining behavioral experiments with survey data collection might wish to use the same platform for the behavioral task as for survey data collection. This example is intended to reduce the barriers to doing so by providing an example of the implementation of a feature that is necessary for many online behavioral experiments but which can be somewhat burdensome to implement in existing software.

Specifically, this example explains how to (a) implement a timer that persists across screens (and/or blocks) of a study while (b) auto-advancing the participant to a post-task screen (c) in Qualtrics. Although these features are trivial to implement in some software specifically designed for behavioral experiments, many researchers find Qualtric offers attractive features that make it desirable for their survey data collection and so end up using multiple platforms.

The example is applicable in nearly all uses cases: any situations where a single question or multiple questions are contained in a single page or multiple pages within a single question block or multiple question blocks. Although here we will assume that the task can be represented in a survey-based response (as is typically the case in choice-based tasks in the social sciences), presumably this can be extended to other cases.

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

Although there is a lot going on in this script, the most important place for you to look is at the bottom where you'll find the following:

- `var timerSeconds = 5`. This indicates that the timer should countdown from 5 secons until 0 seconds. You can change the number of seconds to your designed number here. The script will convert your number of seconds to a more reasonable countdown in terms of minutes and seconds.

- `document.getElementById("timer_1").innerHTML = ""`. This allows you to place any text you would like at the top of the screen once time has elapsed. You can include the message (e.g. "Your time has run out!") in between the quotation marks. For example: `.innerHTML = "Your time has run out!"`. Note that this message will persist at the top of the study unless turned off or set as blank. Here, I've set the field to be empty - meaning that no message is shown once time has elapsed.

- `Qualtrics.SurveyEngine.setEmbeddedData("blockTimeFlag", "1")`. This tells Qualtrics to assign the embedded data field (EDF) named `blockTimeFlag` a value of 1. More on this later.

- `$('NextButton').click();}`. This results in time running out leading to a click of the Next button. This is what will enable use to advance to the end of the task after time has elapsed.

Each of these features (with the exception of the first) can be removed from the above code - perhaps relevant if you don't want to show a message or don't want to advance the screen once time elapses.

# 




Below shows an example of a timing question at the start of the first block of questions in the study. The 

![Alt text](/javascript_added.PNG?raw=true)



in the first question on the experiment's starting page (the first screen one views after clicking through to begin the experiment).

