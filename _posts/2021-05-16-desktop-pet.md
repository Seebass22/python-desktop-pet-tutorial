---
layout: post
title:  "Creating the desktop pet"
---

## prerequisites
- install python, make sure tkinter is installed

## imports
{% highlight  python %}
import tkinter as tk
import time
import random
{% endhighlight %}

We will only need 3 imports. tkinter to draw the window.

## desktop pet
we'll create a class for our pet
{% highlight  python %}
class pet():
    def __init__(self):
{% endhighlight %}

In it's constructor (\__init__), we'll set up the window
{% highlight  python %}
        self.window = tk.Tk()

        self.window.config(highlightbackground='black')
        self.window.overrideredirect(True)
        self.window.attributes('-topmost', True)
        self.window.wm_attributes('-transparentcolor', 'black')
        self.label = tk.Label(self.window, bd=0, bg='black')

        self.window.geometry('128x128+0+0')
        self.label.configure(image=img)
        self.label.pack()

        self.window.after(0, self.update)
        self.window.mainloop()

{% endhighlight %}
