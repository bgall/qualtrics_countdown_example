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

We can add a countdown timer that will persist across multiple pages by using some JavaScript code. First, we should add a 'Timing' type question to the block or page the participant will first see after clicking through to begin the experiment.

![Alt text](/javascript_added.PNG?raw=true "Optional Title")



in the first question on the experiment's starting page (the first screen one views after clicking through to begin the experiment).

