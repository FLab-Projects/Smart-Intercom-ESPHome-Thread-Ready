# Smart-Intercom-ESPHome-Thread-Ready
This project transforms a standard crossbar or digital intercom into a smart device integrated with Home Assistant. Built on an ESP32-C6, it uses the modern OpenThread protocol instead of traditional Wi-Fi.

**Key Features**

• OpenThread (FTD): Operates on the modern Thread mesh network, ensuring low power consumption and high reliability.

• Four operating modes:

  • Once — automatically opens the door on the next call and resets the mode to "Off."
    
  • Always — "passage yard" or open house mode.
    
  • Reject — automatically rejects incoming calls (do not disturb mode).
    
  • Off — manual control via Home Assistant.
    
• Fine-tuning timings: All delays (pickup, open button, ring duration) are variable for easy adaptation to a specific intercom model.

**Hardware**

• Controller: ESP32-C6-DevKitC-1 (chosen for Thread support).

• Actuators: 3 optocouplers for line switching and call detect.

• Passive components (handset emulation):

  • "Answer" resistor (~300–470 Ohm): connected in series with the circuit when relay_answer is triggered, simulating an off-hook handset (speaker impedance).
  
  • "Open" resistor (~1–1.5 kOhm): connected in the same circuit when accept_script is triggered, simulating a press of the open button (increasing line impedance).
  
  • Call detection: Optocoupler based on PC817 or similar, connected to GPIO3.

**Operation Logic (Timings)**

The configuration allows you to adapt the project to any handset using substitutions:

• call_end_detect_delay: end-of-call detection delay (3 sec).

• answer_on_time: simulated "conversation" time before opening the door (1.5 sec).

• open_on_time: duration of pressing the "Open" button (0.6 sec).
