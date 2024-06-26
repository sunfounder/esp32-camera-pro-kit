 .. note::

    Hello, welcome to the SunFounder Raspberry Pi & Arduino & ESP32 Enthusiasts Community on Facebook! Dive deeper into Raspberry Pi, Arduino, and ESP32 with fellow enthusiasts.

    **Why Join?**

    - **Expert Support**: Solve post-sale issues and technical challenges with help from our community and team.
    - **Learn & Share**: Exchange tips and tutorials to enhance your skills.
    - **Exclusive Previews**: Get early access to new product announcements and sneak peeks.
    - **Special Discounts**: Enjoy exclusive discounts on our newest products.
    - **Festive Promotions and Giveaways**: Take part in giveaways and holiday promotions.

    👉 Ready to explore and create with us? Click [|link_sf_facebook|] and join today!

.. _ar_fading:

2.2 Analog Output via PWM
============================

In the previous project, we controlled the LED by turning it on and off using digital output. In this project, we will create a breathing effect on the LED by utilizing Pulse Width Modulation (PWM). PWM is a technique that allows us to control the brightness of an LED or the speed of a motor by varying the duty cycle of a square wave signal.

With PWM, instead of simply turning the LED on or off, we will be adjusting the amount of time the LED is on versus the amount of time it is off within each cycle. By rapidly switching the LED on and off at varying intervals, we can create the illusion of the LED gradually brightening and dimming, simulating a breathing effect.

By using the PWM capabilities of the ESP32 WROOM 32E, we can achieve smooth and precise control over the LED's brightness. This breathing effect adds a dynamic and visually appealing element to your projects, creating an eye-catching display or ambiance.

**Available Pins**

Here is a list of available pins on the ESP32 board for this project.

.. list-table::
    :widths: 5 20 

    * - Available Pins
      - IO13, IO12, IO14, IO27, IO26, IO25, IO33, IO32, IO15, IO2, IO0, IO4, IO5, IO18, IO19, IO21, IO22, IO23



**Required Components**

In this project, we need the following components. 

.. list-table::
    :widths: 30 20
    :header-rows: 1

    *   - COMPONENT INTRODUCTION
        - PURCHASE LINK

    *   - :ref:`cpn_esp32_wroom_32e`
        - |link_esp32_wroom_32e_buy|
    *   - :ref:`cpn_esp32_camera_extension`
        - \-
    *   - Breadboard
        - |link_breadboard_buy|
    *   - Several Jump Wires
        - |link_wires_buy|
    *   - Resistor
        - |link_resistor_buy|
    *   - LED
        - |link_led_buy|


**Schematic**

.. image:: img/circuit_2.1_led.png

This project is the same circuit as the first project :ref:`ar_blink`, but the signal type is different. The first project is to output digital high and low levels (0&1) directly from pin26 to make the LED light up or turn off, this project is to output PWM signal from pin26 to control the brightness of the LED.



**Wiring**

.. image:: img/2.1_hello_led_bb.png


**Code**

#. Download this code or copy this code to the Arduino IDE directly.
    
.. note::
    
    * :ref:`unknown_com_port`

.. raw:: html

    <iframe src=https://create.arduino.cc/editor/sunfounder01/aa898b09-be86-473b-9bfe-317556c696bb/preview?embed style="height:510px;width:100%;margin:10px 0" frameborder=0></iframe>

After the code is uploaded successfully, you can see the LED breathing.

**How it works？**


#. Define constants and variables。

    .. code-block:: arduino

        const int ledPin = 26; // The GPIO pin for the LED
        int brightness = 0;
        int fadeAmount = 5;
   
    * ``ledPin``: The GPIO pin number where the LED is connected (in this case, GPIO 26).
    * ``brightness``: The current brightness level of the LED (initially set to 0).
    * ``fadeAmount``: The amount by which the LED's brightness will change in each step (set to 5).

#. Initializes the PWM channel and configures the LED pin.

    .. code-block:: arduino

        void setup() {
            ledcSetup(0, 5000, 8); // Configure the PWM channel (0) with 5000Hz frequency and 8-bit resolution
            ledcAttachPin(ledPin, 0); // Attach the LED pin to the PWM channel
        }

    Here we use the |link_ledc| (LED control) peripheral which is primarly designed to control the intensity of LEDs, although it can also be used to generate PWM signals for other purposes.

    * ``uint32_t ledcSetup(uint8_t channel, uint32_t freq, uint8_t resolution_bits);``: This function is used to setup the LEDC channel frequency and resolution. It will return ``frequency`` configured for LEDC channel. If 0 is returned, error occurs and ledc channel was not configured.
            
        * ``channel`` select LEDC channel to config.
        * ``freq`` select frequency of pwm.
        * ``resolution_bits`` select resolution for ledc channel. Range is 1-14 bits (1-20 bits for ESP32).


    * ``void ledcAttachPin(uint8_t pin, uint8_t chan);``: This function is used to attach the pin to the LEDC channel.

        * ``pin`` select GPIO pin.
        * ``chan`` select LEDC channel.

#. The ``loop()`` function contains the main logic of the program and runs continuously. It updates the LED's brightness, inverts the fade amount when the brightness reaches the minimum or maximum value, and introduces a delay.

    .. code-block:: arduino

        void loop() {
            ledcWrite(0, brightness); // Write the new brightness value to the PWM channel
            brightness = brightness + fadeAmount;

            if (brightness <= 0 || brightness >= 255) {
                fadeAmount = -fadeAmount;
            }
            
            delay(50); // Wait for 20 milliseconds
            }

    * ``void ledcWrite(uint8_t chan, uint32_t duty);``: This function is used to set duty for the LEDC channel.
        
        * ``chan`` select the LEDC channel for writing duty.
        * ``duty`` select duty to be set for selected channel.