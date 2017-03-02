.. _color_util_ref:

==============
ColorUtilities
==============

Provides conversion utilities for working with different color representations.
Every SmartApp and Device Handler can get a reference to the ``ColorUtilities`` class using the ``getColorUtil()`` method (or the shorthand property reference ``colorUtil``, if you prefer).

.. code-block:: groovy

    def deepSkyBlueInHex = colorUtil.rgbToHex(0, 191, 255)
    log.debug "RGB 0,191,255 in Hex is $deepSkyBlueInHex"

The `ColorUtilities` class works with RGB and hex color values.
A full discussion of web colors is beyond the scope of this document, but the basic definitions used for SmartThings development are defined below.


RGB (Red, Green, Blue)
    The RGB (Red, Green, Blue) color model "is an additive color model in which red, green, and blue light are added together in various ways to reproduce a broad array of colors." [1]_
HEX
    "A hex triplet is a six-digit, three-byte hexadecimal number used in HTML, CSS, SVG, and other computing applications to represent colors. The bytes represent the red, green and blue components of the color. One byte represents a number in the range 00 to FF (in hexadecimal notation), or 0 to 255 in decimal notation." [2]_

----

hexToRgb()
----------

Converts a hex color string to RGB.
Assumes the hex value is three or six characters in length, and may or may not include the leading "#" character.

**Signature:**
    ``static List hexToRgb(String hex)``

**Parameters:**
    `String`_ hex - The hex color string to convert

**Returns:**
    `List`_ - The RGB color representation, ordered as ``[red, green, blue]``

**Example:**

.. code-block:: groovy

    def skyBlueInRgb = colorUtil.hexToRgb('#00BFFF')
    log.debug "sky blue in RGB: $skyBlueInRgb"

----

rgbToHex()
----------

Converts an RGB value to a hexadecimal color string.

**Signature:**
    ``static String rgbToHex(red, green, blue) throws IllegalArgumentException``

**Parameters:**
    `Integer`_ red - The red value, between 0 and 255

    `Integer`_ green - The green value, between 0 and 255

    `Integer`_ blue - The blue value, between 0 and 255

**Returns:**
    `String`_ - The hexadecimal representation of the RGB value

**Throws:**
    `IllegalArgumentException`_ - An ``IllegalArgumentException`` is thrown if any of the RGB values are not within the 0 to 255 range.

**Example:**

.. code-block:: groovy

    def deepSkyBlueInHex = colorUtil.rgbToHex(0, 191, 255)
    log.debug "RGB 0,191,255 in Hex is $deepSkyBlueInHex"

----

.. _IllegalArgumentException: https://docs.oracle.com/javase/7/docs/api/java/lang/IllegalArgumentException.html
.. _Integer: http://docs.oracle.com/javase/7/docs/api/java/lang/Integer.html
.. _List: http://docs.oracle.com/javase/7/docs/api/java/util/List.html
.. _String: http://docs.oracle.com/javase/7/docs/api/java/lang/String.html
.. [1] Wikipedia: https://en.wikipedia.org/wiki/RGB_color_model
.. [2] Wikipedia: https://en.wikipedia.org/wiki/Web_colors
