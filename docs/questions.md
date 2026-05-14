# DFMS — Architecture & Engineering Discussion Reference

---

# SECTION 1 — Motivation & Core Idea

## Q1. What EXACTLY motivated DFMS?

### Answer

The project originated during the SAFE project where repeated USB-based firmware flashing became operationally inefficient and physically difficult because the ESP devices were embedded inside the hardware model.

The repeated process of:

- connecting USB cables
- manually reflashing firmware
- physically accessing devices

became impractical.

This led to exploring wireless firmware update systems and eventually learning about OTA (Over-The-Air) updates used in real-world embedded systems and industries.

The project then evolved from a simple OTA idea into a firmware lifecycle management system focused on:

- deployment reliability
- rollback safety
- update orchestration
- firmware validation
- distributed coordination

---

## Q2. Why is normal/basic OTA insufficient?

### Answer

Basic OTA systems only focus on:

- transferring firmware
- installing firmware successfully

However, real-world firmware management requires:

- rollback handling
- validation mechanisms
- staged rollout strategies
- deployment coordination
- integrity verification
- recovery handling
- update monitoring

DFMS was designed to handle the complete firmware lifecycle rather than only firmware transfer.

---

# SECTION 2 — Communication & Update Strategy

## Q3. Why was polling chosen instead of push-based updates?

### Answer

Polling was chosen because embedded devices may already be performing important operational tasks.

A push-based update mechanism could:

- interrupt running operations
- force the device into update mode unexpectedly
- potentially cause data loss or unsafe interruption

With polling:

- the device completes its current responsibilities
- then checks the server for updates safely
- update timing remains device-controlled

This creates a safer and more controlled update workflow.

---

## Q4. Why HTTP instead of MQTT?

### Answer

HTTP was selected because:

- the current architecture follows a polling-based request-response workflow
- firmware metadata retrieval naturally fits HTTP communication
- HTTP simplifies implementation and debugging during early development

MQTT may later be explored for:

- lightweight update notifications
- trigger-based coordination
- event-driven rollout systems

However, HTTP currently provides a simpler and more manageable architecture for DFMS.

---

# SECTION 3 — Reliability & Rollback

## Q5. Why is A/B partitioning important?

### Answer

Directly overwriting the currently running firmware is risky because:

- faulty firmware
- crashes
- freezes
- invalid firmware
- corrupted downloads

could completely brick the device.

A/B partitioning improves reliability by:

- keeping the currently running firmware untouched
- installing new firmware into an inactive partition
- switching partitions only after successful validation

If the new firmware fails:

- the bootloader reverts to the previous working firmware automatically

This removes the need for manual USB recovery in many situations.

---

## Q6. What makes OTA systems dangerous?

### Answer

OTA systems are dangerous because:

- faulty firmware may break the system completely
- corrupted firmware may fail during runtime
- power loss during updates may interrupt installation
- WiFi or connectivity failures may leave the system in inconsistent states

OTA deployment must therefore consider:

- rollback mechanisms
- validation systems
- connectivity reliability
- safe recovery workflows

OTA should not become the only recovery mechanism available for critical systems.

---

## Q7. Why is post-boot validation necessary?

### Answer

Firmware installation alone does not guarantee that the firmware works correctly.

After reboot:

- the new firmware enters a pending validation state
- runtime initialization and checks are performed
- the firmware must explicitly mark itself as valid

If validation fails:

- rollback is triggered automatically

This ensures that:

- broken firmware does not permanently replace working firmware

---

## Q8. What happens internally during rollback?

### Answer

During rollback:

- the bootloader changes the boot location to the previous firmware partition
- the microcontroller reboots
- the older stable firmware becomes active again

Additionally:

- rollback status and failure information are transmitted back to the server

This allows centralized monitoring of update failures.

---

# SECTION 4 — Firmware Lifecycle Thinking

## Q9. Why does firmware lifecycle management matter?

### Answer

Firmware management is not only about updating firmware randomly.

A proper firmware lifecycle system must manage:

- deployment
- validation
- rollback
- staged rollout
- reliability
- monitoring
- update coordination

DFMS focuses on:

- complete firmware lifecycle orchestration
  rather than:
- simple OTA transfer functionality

---

## Q10. Why is canary deployment important?

### Answer

Canary deployment improves reliability by:

- first deploying firmware to a small subset of devices
- evaluating update success/failure
- monitoring runtime behavior
- deciding whether the update should continue globally

If issues are detected:

- rollout can be stopped
- rollback can occur before affecting all devices

This reduces large-scale deployment risk.

---

# SECTION 5 — Personal Engineering Direction

## Q11. What part of DFMS most reflects your engineering interests?

### Answer

The most interesting part is:

- managing firmware from start to end
- not just updating firmware blindly

The project strongly reflects interests in:

- firmware infrastructure
- deployment systems
- reliability engineering
- embedded lifecycle management
- distributed embedded coordination

---

# SECTION 6 — Engineering Challenges

## Q12. What is technically difficult about DFMS?

### Answer

Current and expected engineering difficulties include:

- building the update server architecture
- distributed coordination
- reliability handling
- firmware storage constraints
- download consistency
- managing rollback logic
- handling interrupted updates
- balancing reliability mechanisms with memory limitations

Another important challenge is:

- integrating safety features without excessive memory overhead on embedded devices

---

# SECTION 7 — Platform & Framework Decisions

## Q13. Why choose ESP32 and ESP8266?

### Answer

ESP32 and ESP8266 were selected because:

- they include built-in WiFi support
- they are affordable and widely available
- they are supported by ESP-IDF
- they provide strong OTA support
- they are practical for distributed embedded deployments

---

## Q14. Why use ESP-IDF instead of Arduino IDE?

### Answer

ESP-IDF was chosen because:

- it provides lower-level access to the ESP32 platform
- it exposes system-level firmware management features
- it allows more control over:
  - partitions
  - OTA systems
  - networking
  - flash management
  - runtime behavior

The Arduino IDE abstracts many low-level details which are important for firmware infrastructure projects like DFMS.

---

## Q15. What role does RTOS play in DFMS?

### Answer

DFMS currently uses the FreeRTOS environment provided through ESP-IDF.

The RTOS environment supports:

- task execution
- OTA workflow coordination
- network operations
- runtime services

However, DFMS is primarily positioned as:

- a firmware infrastructure and reliability project
  rather than:
- an RTOS-focused project.

---

# SECTION 8 — Distributed System Concepts

## Q16. What exactly is distributed in DFMS?

### Answer

Multiple embedded devices:

- run firmware independently
- poll the server independently
- report validation results independently

The server:

- collects update results
- evaluates rollout success/failure
- decides whether global rollout should continue

The distributed aspect comes from:

- coordinated firmware lifecycle management across multiple embedded nodes.

---

# SECTION 9 — Current State vs Future Plans

## Q17. What parts are currently implemented vs planned?

### Answer

The project is currently:

- architecture-heavy
- implementation-growing

The README and architecture documentation define:

- firmware lifecycle workflow
- rollback design
- partition structure
- deployment strategy
- rollout coordination

Many advanced deployment features are still planned for future implementation.

---

## Q18. What firmware safety mechanisms are included?

### Answer

Current and planned safety mechanisms include:

- A/B partition architecture
- checksum verification
- post-boot validation
- rollback handling
- canary deployment
- firmware integrity validation

Future security-focused additions may include:

- digital signatures
- secure boot
- firmware authenticity verification

However, memory constraints are an important concern for advanced security mechanisms.

---

# SECTION 10 — Future Direction

## Q19. What future direction excites you most technically?

### Answer

The most interesting future directions include:

- distributed updates
- embedded Linux-based coordination servers
- scalable rollout orchestration
- advanced firmware infrastructure systems

These directions align strongly with interests in:

- embedded systems
- firmware infrastructure
- distributed embedded coordination
- systems engineering
