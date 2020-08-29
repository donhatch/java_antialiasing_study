Sometimes ["the graphics pen hangs down and to the right from the path it traverses"](https://docs.oracle.com/en/java/javase/11/docs/api/java.desktop/java/awt/Graphics.html), and sometimes it doesn't.
I don't have any clear idea how to predict when it will or won't, but I have observed that RenderingHints.VALUE_STROKE_PURE can sometimes be used to alter the behavior, by trial and error.
In particular, I found that if you turn on STROKE_PURE during your drawPolygon() calls in your program,
it will make them match up with your fillPolygon() calls, as you desire.

I did a little study showing the effect of the STROKE_CONTROL hint:
- STROKE_NORMALIZE (the default, on my system)
- STROKE_PURE

on the following calls:
- drawLine()
- drawPolygon()
- fillPolygon()

in both antialiasing modes:
- ANTIALIASING_OFF
- ANTIALIASING_ON

And there is one more annoying dimension that apparently matters as well:
- rendered directly to a JComponent on the screen
- rendered to a BufferedImage.

Here are the results when rendering directly to a visible JComponent:
[]

And here are the results when rendering through a BufferedImage
[]

(Notice the two cases in which direct rendering differs from BufferedImage rendering:
ANTIALIAS_OFF/STROKE_NORMALIZE/fillPolygon  and ANTIALIAS_OFF/STROKE_PURE/drawPolygon.)

Overall, there doesn't seem to be much overall rhyme or reason to the whole thing.
But we can make the following specific observations based on the above pictures:

Observation #1:
  If you want your antialiased drawPolygon()s and antialiased fillPolygon()s to match up well
  (the original question), then turn on STROKE_PURE during the antialiased drawPolygon() calls.
  (It doesn't matter whether it's on during the antialiased fillPolygon() calls.)
Observation #2:
  If you want your antialiased fillPolygon()s and *non*-antialiased fillPolygon()s
  to match up (because, say, your app allows the user to switch antialiasing on and off,
  and you don't want the picture to jump northwest and southeast each time they do that),
  then turn on STROKE_PURE during the non-antialiased fillPolygon() calls.

Here is the program I used.
My results are from compiling and running it it with opensdk11 on linux;
I'd be interested to know if anyone gets any different results on different platforms.
[]
