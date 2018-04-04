---
layout: post
title: Trodes 1.7.2
subtitle: New features, bug fixes, and important changes
---

Release 1.7.2 is out as of Monday, March 26. Two months ago, when 1.7.0 was released, there wasn't any posts or release notes about it. However, after two months of receiving feedback from our amazing customers, we have released minor revisions 1.7.1, followed by 1.7.2 with bug fixes and feature modifications.

In addition to starting a massive Slack workspace for all of our customers, we are also experimenting with posting these updates on here, our Github page. As always, feedback is welcome and greatly appreciated! - Kevin

- [Major features](#major-features)
    - [NTrodes selections and settings](#ntrodes-selections-and-settings)
        - [New settings panel](#new-settings-panel)
        - [Selecting multiple ntrodes](#selecting-multiple-ntrodes)
    - [Categories and tags for ntrodes](#categories-and-tags-for-ntrodes)
    - [Stream and spike display](#stream-and-spike-display)
        - [Stream display](#stream-display)
        - [Spike display](#spike-display)

## Major features

### Hardware connection indicators

We have added an indicator bar next to the recording one for your hardware source connection. If something is wrong, it will flash red with a message on it. Otherwise, it will remain white.

![Connection ok](/img/connectionok.png)

![Connection err](/img/connectionerr.png)

In addition to the indicator, we have also added code to have the system attempt to reconnect to the hardware if it ever briefly disconnects. If it does not connect again immediately, the error will flash.

### NTrodes selections and settings

Previously, if you wanted to change the settings of multiple ntrodes, you had two choices: change each individual ntrode at a time, or use the "Link Ntrodes" button to change all of them at once. There was no in between. Now, with our newest feature, we have made it easier to change settings.

#### New settings panel

We have added a new settings panel. As the settings list for ntrodes grew, we felt the need to move it into its own dialog. Changes are applied immediately upon selection. Like before, only filters and display settings are available to be changed, since they do not affect the raw, unfiltered data recorded.

![Ntrode settings button](/img/ntrodebutton.png)

![Ntrode settings panel](/img/ntrodesettingspanel.png)

#### Selecting multiple ntrodes

Using CTRL or SHIFT, you can now select multiple ntrodes to change settings. If any settings are different across the selected ntrodes, they will be highlighted red. This is not an error, just an indication that they're different. Any changes to the settings will automatically be applied to all selected ntrodes.

![Multi select ntrodes](/img/trodesmultiselect.png)

### Categories and tags for ntrodes

If you looked at the ntrode settings panel, you may have noticed an option to add categories/tags. Ntrodes can now be tagged with categories and tags. These can be considered as behind-the-scenes labels. Right now, you can add these custom "labels" to any ntrodes and use them to select a subset of ntrodes and subsequently change settings for them. There will be room to expand this feature in the future, such as using the labels as metadata when exporting or in NWB format.

### Stream and spike display

#### Stream display

You can now select different views for the stream display. Previously, the default was spike filtered data. If you want to see LFP or unfiltered data, select it from the "Stream display" button. The LFP view only shows one channel per ntrode, and that value can be changed in the ntrode settings panel.

![Stream display button](/img/streamdisplaybutton.png)

#### Spike display

Since the last release 1.6, you can view all waveforms at once. Clusters will color each individual waveform as well, so you can see the waveforms grouped up by cluster. As of this release, you can also choose to hide unclustered data as well as enable/disable individual clusters. This functionality makes it easier to view and manually edit the clusters in trodes.

![Spike display, all clusters](/img/spikedisp1.png)

![Spike display, some clusters](/img/spikedisp2.png)
