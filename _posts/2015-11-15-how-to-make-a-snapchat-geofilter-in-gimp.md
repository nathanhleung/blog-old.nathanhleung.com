---
layout: post
title: How to Make a Snapchat Geofilter in GIMP
author: Nathan Leung
comments: true
tags: [misc]
---
If you've ever opened Snapchat and took a picture, then swiped right, chances are that you’ve seen one of their geofilters.
Ever wondered how to make one?

To make our filter, we’ll be using a program called GIMP.  First, open GIMP and create  a new image, entering “1080” for
image width and “1920” for height.  This is the standard size for geofilters and Snapchat won’t accept your filter if 
doesn’t exactly match the dimensions.  After you click OK, you’ll see a new image with a white background.  However, 
Snapchat only accepts filters with transparent backgrounds.  To make the background transparent, in the “Layers” window 
on the right, click the Eye button on the “Background” layer.  This will prevent the white background from showing up when 
you export the image.  Next, (still on the “Layers” window), at the bottom (above “Brushes”), click the Paper icon, which
will create a new layer.  Name the layer “Outline” (you’ll see why later).

This is the layer in which you do all the filter art.  In this article, we’ll go over making a basic text filter but 
feel free to do something else—this is the step that requires the most creativity.  Anyways, to begin, click the “Text” 
tool and type something, changing the font and color to your liking.  After you’ve got some text down that you’re satisfied
with, switch to the Select tool (the dotted rectangle in the Toolbox on the left), which will deselect the text.

Now, right click on the newly created text layer (the layer name should match the text you type) and click “Text to Path.”
After that, click on the layer you created initially, “Outline,” to switch to it.  Next, click the Select menu in the upper
left and click “From Path.”  This will create a selection that’s the shape of the text, but on the layer below it.
Next, to create the outline, click the Select menu again and click “Grow.”  In the Grow menu, start with 5px and click OK,
then fill the newly increased selection with the outline color of your choice.  This should give your text a good, solid
outline.  If it’s too big or small, you can undo back to the original selection and try growing it to another size.

![Making a Geofilter in GIMP](http://i.imgur.com/LPorWnB.png)

Once you’re finished making your design, you need to export your filter.  To do that, click “File,” then “Export As.”
When you click “Export,” GIMP will create a PNG file that you can send to Snapchat.  Make sure the file’s a PNG file,
because Snapchat won’t accept any other formats.

With the exported file, head over to the [geofilter submission page](https://www.snapchat.com/geofilters/submit.html).
Once you’re there, click on the Search Box to find the location you made the filter for, and begin clicking the map to
plot some points.  Once you’re satisfied with your geofence (make sure it’s not too big), upload your geofilter.  If you
get an error saying that “your geofilter must be under 500kb,” run the file through [TinyPNG](https://tinypng.com/)
and reupload (TinyPNG reduces the file size of your image by reducing the number of colors in it).

After uploading your filter, fill in the necessary info (name, email, why is the location special) and check all the boxes.
By the way—the “why is this location meaningful to you” text area doesn’t have to be that elaborate.  For example,
“This is my high school and its surrounding athletic fields and grounds. Many people send snaps here.” would work for a
school-wide geofilter.

Anyways, now you can click “Submit.”  You’ll immediately get a confirmation email from Snapchat saying “Geofilter Submission
Successful,” but the actual acceptance/rejection email will come within three to five days.  If you don’t get accepted,
don’t worry—most of the time, a few minor tweaks such as adding an outline to your filter or editing the geofence area
will be sufficient and you’ll get accepted on your next submission.  Finally, when you do get accepted, don’t forget to
share the filter with your friends and post it on your Story.  It’s all about the Snap Story!
