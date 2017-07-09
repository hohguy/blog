# Accessible PayPal Buttons

Recently, I have been helping a local non-profit with their webpage design.

Their webpage had several accessibility issues to address. To my surprise the biggest issue was a form. Not just any form. A form that was provided by PayPal.

Imagine for a moment that you have a small business and you want to sell stuff on the web. So you setup a PayPal Business account. Now you want folks to buy stuff from your website.

Those of you who have had any experience with a Paypal Business account will know that PayPal provides business owners with a PayPal buttons feature that will help you set up everything you need to sell stuff through your website. Once you finish with the set up, PayPal will give you all the HTML code you need ready to paste into your site.

Something like this, but not formatted:

```html
    <form action="https://www.paypal.com/cgi-bin/webscr" method="post" target="_top">
    <input type="hidden" name="cmd" value="_s-xclick">
    <input type="hidden" name="hosted_button_id" value="[merchant code]">
    <table>
        <tr><td><input type="hidden" name="on0" value="I want:">I want:</td></tr>
        <tr><td><select name="os0">
        	<option value="Hat">Hat $10.00 CAD</option>
        	<option value="Scarf">Scarf $20.00 CAD</option>
            </select>
          </td></tr>
        <tr><td><input type="hidden" name="on1" value="What colour?">What colour?</td></tr>
        <tr><td><select name="os1">
        	<option value="Red">Red </option>
        	<option value="Blue">Blue </option>
            </select>
          </td></tr>
        <tr><td><input type="hidden" name="on2" value="Customize">Customize</td></tr>
        <tr><td><input type="text" name="os2" maxlength="200"></td></tr>
      </table>
    <input type="hidden" name="currency_code" value="CAD">
    <input type="image" 
        src="https://www.paypalobjects.com/en_US/i/btn/btn_paynow_LG.gif" border="0" name="submit"
        alt="PayPal - The safer, easier way to pay online!">
    <img alt="" border="0" src="https://www.paypalobjects.com/en_US/i/scr/pixel.gif" width="1" height="1">
    </form>
```

And there lies the problem. PayPal’s automatically generated code is not accessible.

## What are the accessibility issues?

There are three major issues which impact the accessibility of the form:

1. It uses a table for layout. The three form items are laid out over six rows. Tables are for data, not layout. 
2. The form has no labels.
3. The button's `alt` attribute text does not specify the purpose and target of the button.

## Using a table for layout

There are better ways to both design websites and provide semantic information to non-visual users than using a table. By not using a table, our form can be responsive and mobile-friendly. I suggest using either `<div>'s` or a list.

The easy way to fix this accessibility problem is to replace the table with an unordered list. Because a screen reader will describe the list as the user navigates into the form, using a list provides useful semantic information to non-visual users such as how many parts are in the form and where they are in the form.

If I replace the table with a list and remove some redundant table rows, my form looks like this:
```html
    <form action="https://www.paypal.com/cgi-bin/webscr" method="post" target="_top">
    <input type="hidden" name="cmd" value="_s-xclick">
    <input type="hidden" name="hosted_button_id" value="[merchant code]">
    <ul>
        <li><input type="hidden" name="on0" value="I want:">I want:
        <select name="os0">
        	<option value="Hat">Hat $10.00 CAD</option>
        	<option value="Scarf">Scarf $20.00 CAD</option>
            </select>
          </li>
        <li><input type="hidden" name="on1" value="What color?">What color?
        <select name="os1">
        	<option value="Red">Red </option>
        	<option value="Blue">Blue </option>
            </select>
         </li>
        <li><input type="hidden" name="on2" value="Customize">Customize
            <input type="text" name="os2" maxlength="200">
          </li>
      </ul>
    <input type="hidden" name="currency_code" value="CAD">
    <input type="image"
        src="https://www.paypalobjects.com/en_US/i/btn/btn_paynow_LG.gif" border="0" name="submit"
        alt="PayPal - The safer, easier way to pay online!">
    <img alt="" border="0" src="https://www.paypalobjects.com/en_US/i/scr/pixel.gif" width="1" height="1">
    </form>
```
Now I have three list items, one for each part of the form.

Using simple CSS, we can remove the bullets and style the list in any other way we want. For example, to remove the bullets,
```css
    li { list-style-type: none; }
```

## Adding labels

Visually, the form looks like its ready to go! But, there is still some under-the-hood stuff that we need to fix.

All of those form prompts are bare text. While it may be obvious visually which prompt goes with which form item, it is not semantically clear. For this reason we need to add labels.

There are two ways that labels can be used to make a form accessible.

The first is to wrap the form input into the label. Note the addition of an `id` value on the input and a `for` on the label. This approach provides the added benefit of a larger area for pointing devices (or fingers) to select the form item.
```html
        <li><label for="cust">
            <input type="hidden" name="on2" value="Customize">Customize
            <input id="cust" type="text" name="os2" maxlength="200">
            </label>
          </li>
```
The other option is to not wrap the input, only the prompt. Again, note the addition of an `id` attribute on the input and a `for` on the label.
```html
        <li><input type="hidden" name="on2" value="Customize">
            <label for="cust">Customize</label>
            <input id="cust" type="text" name="os2" maxlength="200">
          </li>
```

Wrapping the input with the label makes clear what part of the form the label is for. Adding the `id`/`for` attributes will also cue the screen reader to know what part of the form goes with the label.

Because different screenreaders have different behaviour with the same code, it is important to have multiple cues. In the second example, I also recommend using an `aria-label`.
```html
    <li><input type="hidden" name="on2" value="Customize">
        <label for="cust">Customize</label>
        <input id="cust" aria-label="Customize" type="text" name="os2" maxlength="200">
        </li>
```

There are many resources on the web for [more information about ARIA and aria-labels](https://developers.google.com/web/fundamentals/accessibility/semantics-aria/aria-labels-and-relationships).  

## Improving the button

Personally, I always recommend using a CSS button or the default HTML button provided by the browser than a graphic button like the one PayPal recommends. These kinds of buttons allow screenreaders to better interpret the intent and the text of the button.

Here is an HTML button. This code:
```html
<input type="submit" value="Donate">
```
Creates this button:
<input type="submit" value="Donate">

In the case of images, the `alt` attribute provides text as an "alternative" to an image in case someone cannot see the image (e.g., because of a broken link, slow connection, screenreader, etc.). The text should be short, informative, and descriptive where the image provides information. It should be no more than the length of a tweet. Where the image is purely decorative, there should be no text.

Buttons provide a function. Thus, when using an image for a button, the `alt` attribute text needs to tell the user what the button does and/or where it goes. When using an image button, as PayPal seems to recommend, the `alt` attribute provides the button label.

By default, PayPal provides the following `alt` attribute text with their code:
```html
alt="PayPal - The safer, easier way to pay online!"
```

But, the button might look like this:
![Example PayPal buttons](https://desk-customers.s3.amazonaws.com/webforce/assets/114311/std_checkout_original.png)

These buttons have text like, "Buy Now", "Donate", and "Add to Cart". The default `alt` text does not change to reflect the different buttons' labels. The text on these images is buried in the image and not available to anyone who cannot see the image.

Worse, what happens if you have two PayPal buttons on the page? They will both have the same default `alt` attribute text and thus they will be indistinguishable to the non-visual user.

The solution is to make the `alt` attribute text the same as the button label. For example:

For this button,

![A PayPal Donate button](http://www.freepngimg.com/thumb/paypal_donate_button/1-2-paypal-donate-button-png-thumb.png)

You might use this `alt` attribute text:
```html
alt="Donate with PayPal"
```


## Final notes

A few other thoughts that might help with your PayPal button form: 
* The form can be reordered however you like. PayPal only needs the form variables (e.g., `name="on1"`) to remain unchanged.
* Make sure the rest of your website is accessible too.

Making a few simple changes to the code provided by the PayPal button tool will make all the difference to your customers. An accessible form along with accessible buttons will make the experience of doing business with your business easier for everyone.
