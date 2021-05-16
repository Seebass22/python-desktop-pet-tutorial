---
layout: post
title:  "Creating the desktop pet"
---

## prerequisites
Make sure you have python and tkinter installed. Test if tkinter is installed with `python -m tkinter`. It should open a demo window. If not, you may have to run the python installer again and make sure you have the "install tkinter/IDLE" box checked.

## imports
{% highlight  python %}
import tkinter as tk
import time
import random
{% endhighlight %}

We will only need 3 imports. tkinter is python's standard GUI toolkit, which we'll use for drawing the window. We'll need the others later.
This is not an in-depth tkinter tutorial, so refer to the [tkinter documentation](https://docs.python.org/3/library/tkinter.html) if you really want to understand what the code does.

## desktop pet
we'll create a class for our pet
{% highlight  python %}
class pet():
    def __init__(self):
{% endhighlight %}

In it's constructor (\__init__), we'll set up the window and use a placeholder image.
{% highlight  python %}
        # placeholder image
        img = tk.PhotoImage(file='placeholder.png')

        # create a window
        self.window = tk.Tk()

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

Then we'll write a function that will be called continuously once the mainloop starts (it doesn't have to be called update).
Calling `.after()` on our window does not by itself cause the function to be called in a loop. To achieve this, we must have the function
call itself the same way.

{% highlight  python %}
        def update(self):
            # add code here
            self.window.after(10, self.update)
{% endhighlight %}
