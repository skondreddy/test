---
layout: default
title: Submitting From the Local Machine
nav_order: 3
parent: Monitoring Jobs
---

# Submitting from the local machine

When we are unable to submit because of a problem with the submission EJB and
* We are unable to fix the problem before the end of trading
* We are unable to stand-up and shift to a replacement EJB service

Then we can by pass the submission EJB and have the traders submit from
their local machine.

### Turn off submission by EJB
1. Run AppConfigEditor.
2. Select the COOLAPP domain.
3. Modify the required SUBMIT\_*{ISO}*\_VIA\_EJB properties to 0.

### Refresh properties in CoolApp
Each trader must Refresh Properties in CoolApp using these steps:

1. In **CoolApp** on the main window. Find the **Refresh Properties** button   in the **Refresh Properties** section in the top display bar near the right hand side.
2. Click the **Refresh Properties** button.

The submission process will now run on the local machine.

### Developer submitting for traders
In the event that a trader is unable to refresh the properties in CoolApp,
the On-Call developer can set their CoolApp for local submissions and submit for any traders that can not process their own.

