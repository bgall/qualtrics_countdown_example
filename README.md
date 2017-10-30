# Example Cross-Page Countdown Timer with Auto-Advance

Although software abounds for the implementation of online, timed behavioral studies, many researchers combining behavioral experiments with survey data collection might wish to use the same platform for the behavioral task as for survey data collection. This example is intended to reduce the barriers to doing so by providing an example of the implementation of a feature that is necessary for many online behavioral experiments but which can be somewhat burdensome to implement in existing software.

Specifically, this example explains how to (a) implement a timer that persists across screens (and/or blocks) of a study while (b) auto-advancing the participant to a post-task screen (c) in Qualtrics. Although these features are trivial to implement in some software specifically designed for behavioral experiments, many researchers find Qualtric offers attractive features that make it desirable for their survey data collection and so end up using multiple platforms.

The example is applicable in nearly all uses cases: any situations where a single question or multiple questions are contained in a single page or multiple pages within a single question block or multiple question blocks. Although here we will assume that the task can be represented in a survey-based response (as is typically the case in choice-based tasks in the social sciences), presumably this can be extended to other cases.

## The Demonstration-Case

For demonstration purposes, we'll focus on a concrete example. In this example, study participants have a limited amount of time (5 minutes) to decode as many codes as they can. On each screen, they are provided with some codes and table that provide the crosswalk between coded values and decoded values. They can enter the decoded values on the same screen and can move forwards or backwards between screens throughout the time limit. At the end of the time limit, they will be forwarded to a post-experiment screen. We will assume that each screen is a different block of values/crosswalks/answer spaces - although this is not necessarily - and that the researcher does not want the participant to advance beyond last page of questions prior to the end of the time window.^[er]

Once participants click through to the start of the experiments, we want to ensure that the participants can track the amount of time remaining on the task. In such cases, providing a clear countdown timer at the top of the screen is useful. Additionally, we wanted to randomize the words the participants viewed and limit the number of 

we have many blocks of survey questions or choice-tasks that comprise a single study. The questions within each block can be located on a single page or multiple pages. For simplicity, we'll assume you have a single page of questions within each of many blocks.

# Setting up your survey

You'll first want to create all of your survey questions. 

