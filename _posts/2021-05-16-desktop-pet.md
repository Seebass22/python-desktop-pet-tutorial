---
layout: post
title:  "Creating the desktop pet"
---

## prerequisites
This code is windows-specific. It will not run on linux (I've tried).

Make sure you have python and tkinter installed. Test if tkinter is installed with `python -m tkinter`. It should open a demo window. If not, you may have to run the python installer again and make sure you have the "install tkinter/IDLE" box checked.

## imports
{% highlight  python %}
import tkinter as tk
import time
{% endhighlight %}

We will only need 2 imports. tkinter is python's standard GUI toolkit, which we'll use for drawing the window.
This is not an in-depth tkinter tutorial, so refer to the [tkinter documentation](https://docs.python.org/3/library/tkinter.html) if you really want to understand what the code does.

## desktop pet
we'll create a class for our pet
{% highlight  python %}
class pet():
    def __init__(self):
{% endhighlight %}

In it's constructor (\__init__), we'll set up the window and use a placeholder image.
{% highlight  python %}
        # create a window
        self.window = tk.Tk()

        # placeholder image
        img = tk.PhotoImage(file='placeholder.png')

        # set focushighlight to black when the window does not have focus
        self.window.config(highlightbackground='black')

        # make window frameless
        self.window.overrideredirect(True)

        # make window draw over all others
        self.window.attributes('-topmost', True)

        # turn black into transparency
        self.window.wm_attributes('-transparentcolor', 'black')

        # create a label as a container for our image
        self.label = tk.Label(self.window, bd=0, bg='black')

        # create a window of size 128x128 pixels, at coordinates 0,0
        self.window.geometry('128x128+0+0')

        # add the image to our label
        self.label.configure(image=img)

        # give window to geometry manager (so it will appear)
        self.label.pack()

        # run self.update() after 0ms when mainloop starts
        self.window.after(0, self.update)
        self.window.mainloop()
{% endhighlight %}

Then we'll write a method that will be called continuously once the mainloop starts (it doesn't have to be called update).
Calling `.after()` on our window does not by itself cause the function to be called in a loop. To achieve this, we must have the method call itself the same way.

{% highlight  python %}
        def update(self):
            # add code here
            self.window.after(10, self.update)
{% endhighlight %}

Finally, we'll call the constructor
{% highlight  python %}
pet()
{% endhighlight %}

![img]({{ site.url }}/assets/placeholder.png)
If you run the code now, and put an image named "placeholder.png" in your folder (such as the stickman here), you should have your character appear in the top left of your screen.

Next let's make the character move.
## making the character move

We'll want to keep track of the character's x position and draw him there.
Replace the call to `self.window.geometry()` with this:

{% highlight  python %}
        # create a window of size 128x128 pixels, at coordinates 0,0
        self.x = 0
        self.window.geometry('64x64+{x}+0'.format(x=str(self.x)))
{% endhighlight %}

...and fill in the `update()` method:
{% highlight  python %}
    def update(self):
        # move right by one pixel
        self.x += 1

        # create the window
        self.window.geometry('64x64+{x}+0'.format(x=str(self.x)))
        # add the image to our label
        self.label.configure(image=self.img)
        # give window to geometry manager (so it will appear)
        self.label.pack()

        self.window.after(10, self.update)
{% endhighlight %}

To move the character, we call `self.window.geometry()` and fill in our new x position.
We will again have to call `self.label.pack()`.

If you run the code now, the character should move to the right.

He's not animated yet, so let's do that next.

## animating the character
![img]({{ site.url }}/assets/walking_right.gif)

You can use this terrible walking animation I made if you don't have one yet.

Replace the call to `tk.PhotoImage()` in the constructor with the gif:
{% highlight  python %}
        # placeholder image
        self.walking_right = [tk.PhotoImage(file='walking_right.gif', format='gif -index %i' % (i)) for i in range(4)]
{% endhighlight %}

This line uses a [list comprehension](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions)
to create a list, where every element is the result of calling `tk.PhotoImage()`, with a different index
to select each frame of the gif. If your gif has a different amount of frames, you'll need change the `range()` call.

We'll want to keep track of the index of the current frame and assign the frame to a variable.
We'll also want to keep track of how much time has passed since the frame has advanced.
{% highlight  python %}
        self.frame_index = 0
        self.img = self.walking_right[self.frame_index]

        # timestamp to check whether to advance frame
        self.timestamp = time.time()
{% endhighlight %}

Lastly, replace the whole update method with this one:
{% highlight  python %}
    def update(self):
        # move right by one pixel
        self.x += 1

        # advance frame if 50ms have passed
        if time.time() > self.timestamp + 0.05:
            self.timestamp = time.time()
            # advance the frame by one, wrap back to 0 at the end
            self.frame_index = (self.frame_index + 1) % len(self.walking_right)
            self.img = self.walking_right[self.frame_index]

        # create the window
        self.window.geometry('64x64+{x}+0'.format(x=str(self.x)))
        # add the image to our label
        self.label.configure(image=self.img)
        # give window to geometry manager (so it will appear)
        self.label.pack()

        self.window.after(10, self.update)
{% endhighlight %}

Here we check if 50ms have passed since we last advanced the frame and only advance it then.
To keep our frame index within the bounds of the amount of frames we have, we use the modulo (`%`)
operator to get the remainder of an integer division by the number of frames (the length of our array that
contains the frames).

Our character should now move right and be animated! From here, it's up to you to add more logic.
Here are some ideas:
- also moving left
- states, such as idling, jumping, etc.
- switching between states at random
- interaction with the mouse cursor

## the final code

{% highlight  python %}
import tkinter as tk
import time
import random

class pet():
    def __init__(self):
        # create a window
        self.window = tk.Tk()

        # placeholder image
        self.walking_right = [tk.PhotoImage(file='walking_right.gif', format='gif -index %i' % (i)) for i in range(4)]
        self.frame_index = 0
        self.img = self.walking_right[self.frame_index]

        # timestamp to check whether to advance frame
        self.timestamp = time.time()

        # set focushighlight to black when the window does not have focus
        self.window.config(highlightbackground='black')

        # make window frameless
        self.window.overrideredirect(True)

        # make window draw over all others
        self.window.attributes('-topmost', True)

        # turn black into transparency
        self.window.wm_attributes('-transparentcolor', 'black')

        # create a label as a container for our image
        self.label = tk.Label(self.window, bd=0, bg='black')

        # create a window of size 128x128 pixels, at coordinates 0,0
        self.x = 0
        self.window.geometry('64x64+{x}+0'.format(x=str(self.x)))

        # add the image to our label
        self.label.configure(image=self.img)

        # give window to geometry manager (so it will appear)
        self.label.pack()

        # run self.update() after 0ms when mainloop starts
        self.window.after(0, self.update)
        self.window.mainloop()

    def update(self):
        # move right by one pixel
        self.x += 1

        # advance frame if 50ms have passed
        if time.time() > self.timestamp + 0.05:
            self.timestamp = time.time()
            # advance the frame by one, wrap back to 0 at the end
            self.frame_index = (self.frame_index + 1) % 4
            self.img = self.walking_right[self.frame_index]

        # create the window
        self.window.geometry('64x64+{x}+0'.format(x=str(self.x)))
        # add the image to our label
        self.label.configure(image=self.img)
        # give window to geometry manager (so it will appear)
        self.label.pack()

        self.window.after(10, self.update)

pet()
{% endhighlight %}
