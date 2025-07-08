**Marathon Run Time Recorder**

This project is a **real-time RFID-based runner tracking system** that logs marathon participant data and automatically sends it to a **Google Form** using Wi-Fi. It is designed to be deployed at marathon checkpoints to read RFID cards and record runner times seamlessly over the internet.

**Features**

* **RFID Tag Reading** via MFRC522 module
* **WiFi Connectivity** to send data online
* **Google Form Integration** for seamless data recording
* **Block-level RFID authentication and reading**
* Designed for **ESP8266-based boards**

**Hardware Requirements**

* ESP8266-compatible board (e.g., NodeMCU)
* MFRC522 RFID Reader Module
* RFID Tags/Cards
* Internet-connected Wi-Fi network

**Project Structure**

* Core logic to:

  * Initialize RFID and Wi-Fi
  * Read runner ID from RFID tag (block 2)
  * Post the data to a linked Google Form
* `GoogleFormPost.h`: Helper class to handle form data packaging and HTTP POST

**Circuit Connections**

**Connected MFRC522 Pin to ESP8266 (NodeMCU)**

SDA -> D5 (GPIO5)

SCK -> D6 (GPIO12)

MOSI -> D7 (GPIO13)

MISO -> D8 (GPIO15)


RST -> D3 (GPIO0)

3.3V -> 3V3

GND -> GND

Make sure `SS_PIN = 5` and `RST_PIN = 0` as per code.

**Google Form Setup**

1. Create a Google Form with **one short-answer field** for runner ID (or name).
2. Get the **form URL** and optionally the **field entry ID**:

   * Example:

     FORM_ROOT_URL = "https://docs.google.com/forms/d/e/1FAIpQLScIeEk0yyfLJE0MnUDdoK72y34nS4e2t_NQdtnfg7ITc_jFZQ/viewform"
    
3. Optionally inspect the field name (e.g., `entry.333496518`) using browser dev tools if you want to post using field ID.

**WiFi Setup**

const char *ssid = "YourWiFiSSID";
const char *password = "YourWiFiPassword";

**How It Works**

1. **Setup Phase**:

   * Connects to Wi-Fi
   * Initializes RFID module

2. **Loop**:

   * Waits for RFID card
   * Reads Block 2 of the tag
   * Sends the content as a response to the Google Form
   * Only posts once per boot (controlled by `postOnce` flag)

**Function Highlight**

### `ReadDataFromBlock(int blockNum, byte readBlockData[])`

Reads 16 bytes of data from the specified block after authenticating using default key (0xFF...).

**Important Notes**

* Ensure your RFID cards have preloaded data at Block 2.
* The `str16[]` char array stores the 16-byte content read from the card.
* Posting happens **only once per power cycle**; change `postOnce` logic to allow repeated submissions.

**Example Serial Output**


Connecting to WiFi...

Connected to WiFi

Reading last data from RFID...

Authentication success

Block was read successfully

Last data in RFID:2 --> BIB123456789012

POST URL: https://docs.google.com/forms/d/...

Data sent successfully!
