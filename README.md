# ESP23-Micro-ros
This is a Flask-based web application to control an LED connected to an ESP32. The app allows users to turn the LED on or off and check its status using a web interface.
Features

    Turn LED ON: Sends a request to the ESP32 to turn the LED on.
    Turn LED OFF: Sends a request to the ESP32 to turn the LED off.
    Check LED Status: Displays the current status of the LED.

Requirements

    Python 3.6+
    Flask
    ESP32 with a connected LED
    Local network setup with ESP32 accessible via its IP address

Installation
Clone the repository:

git clone https://github.com/thississid/Connecting-ESP32-MICROROS.git
cd esp32-led-control

Install the required dependencies:

pip install flask requests

Update the esp32_ip variable in the code with your ESP32's IP address:

esp32_ip = "http://<ESP32_IP_ADDRESS>"

Run the Flask application:

python app.py

Open your browser and go to

http://127.0.0.1:5000.

Usage

    Open the web app in your browser.
    Click on "Turn LED ON" to turn the LED on.
    Click on "Turn LED OFF" to turn the LED off.
    Click on "Check LED Status" to see if the LED is currently on or off.

ESP32 Setup

    Flash your ESP32 with firmware that handles requests to /LED=ON and /LED=OFF.
    Ensure your ESP32 is connected to the same network as the computer running this app.

Example ESP32 Code
Below is an example of how your ESP32 code might look:

 #include <WiFi.h>

 // Replace with your WiFi credentials
 const char* ssid = "";       // Your WiFi SSID
 const char* password = ""; // Your WiFi password

 WiFiServer server(80); // Create a web server on port 80

 void setup() {
 Serial.begin(115200);

 // Connect to WiFi
 WiFi.begin(ssid, password);
 Serial.print("Connecting to WiFi...");
 while (WiFi.status() != WL_CONNECTED) {
     delay(500);
     Serial.print(".");
 }
 
 Serial.println("\nConnected to WiFi!");
 Serial.print("IP Address: ");
 Serial.println(WiFi.localIP());

 // Start the server
 server.begin();
 Serial.println("Server started.");

 // Initialize onboard LED
 pinMode(LED_BUILTIN, OUTPUT);
 digitalWrite(LED_BUILTIN, LOW); // Ensure LED is off initially
 }

 void loop() {
 // Check if a client is connected
 WiFiClient client = server.available();
 if (client) {
     Serial.println("New client connected.");
     String request = "";
     
     // Read the client request
     while (client.connected()) {
     if (client.available()) {
         char c = client.read();
         request += c;

         // Break out of loop once the request ends
         if (request.endsWith("\r\n\r\n")) break;
     }
     }

     // Debug: Print the request to the Serial Monitor
     Serial.println(request);

     // Parse the request for LED control
     if (request.indexOf("/LED=ON") != -1) {
     digitalWrite(LED_BUILTIN, HIGH); // Turn ON the LED
     Serial.println("LED turned ON");
     } else if (request.indexOf("/LED=OFF") != -1) {
     digitalWrite(LED_BUILTIN, LOW);  // Turn OFF the LED
     Serial.println("LED turned OFF");
     }

     // Send an HTTP response to the client
     client.println("HTTP/1.1 200 OK");
     client.println("Content-Type: text/html");
     client.println("Connection: close");
     client.println();
     client.println("<!DOCTYPE HTML>");
     client.println("<html>");
     client.println("<h1>ESP32 LED Control</h1>");
     client.println("<p><a href=\"/LED=ON\">Turn LED ON</a></p>");
     client.println("<p><a href=\"/LED=OFF\">Turn LED OFF</a></p>");
     client.println("</html>");

     // Close the connection
     client.stop();
     Serial.println("Client disconnected.");
 }
 }

Why Use PlatformIO?
Easy Setup:

    Simplifies the configuration of toolchains and libraries for embedded platforms.
    Wide Compatibility: Supports over 1,000 embedded boards and 35+ development frameworks.

Advanced Features:

    Dependency management
    Debugging tools
    Continuous integration support

Multi-Platform Development:

    Work across different platforms (Linux, macOS, and Windows).
