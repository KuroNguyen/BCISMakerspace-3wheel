=============================================================================
README.txt - Decawave's DW1000 Application Programming Interface (API)
=============================================================================

The DW1000 API package is composed of the following folders:

    - decadriver:
    
      Driver for DW1000 UWB transceiver IC.. Details about each function can
      be found in DW1000 API Guide.

    - examples:
    
      A set of individual (simple) examples showing how to achieve different
      functionalities, e.g. sending a frame, receiving a frame, putting the
      DW1000 to sleep, two-way ranging.  The emphasis of theses examples is
      to be readable with explanatory notes to explain and show how the main
      features of the DW1000 work and can be used.

    - Libraries, Linkers:
    
      Hardware abstraction layer (system start-up code and peripheral
      drivers) for ARM Cortex-M and ST STM32 F1 processors. Provided by ST
      Microelectronics.
      
    - platform:

      Platform dependant implementation of low-level features (IT management,
      mutex, sleep, etc).

Please refer to DW1000 API Guide accompanying this package for more details
about provided API and examples.

NOTE: The DW1000 API/driver code included in this package is an unbundled
      version of the DW1000 API/driver. This version may be different to
      (generally by being newer than) those bundled with Decawave's other
      products.

=============================================================================
=                                                                           =
=                               Release Notes                               =
=                                                                           =
=============================================================================

=============================================================================
Package v2.04 / Driver v4.0.6  (7th April 2017)
=============================================================================

a) updated the API version number to 4.0.6 

b) Added new dwt_readcarrierintegrator() API call to read the DW1000 Carrier
   Integrator value and #define constants to compute clock offset between
   local RX and remote TX device.

   Note: register location of Carrier Integrator value is not published in
	 the DW1000 User Manual (v2.10) should be added to next revision.

c) Modified the single-sided two-way ranging example (ex_06a_ss_twr_init) to
   use the new dwt_readcarrierintegrator() API to ascertain the responder's
   clock offset and compensate for this in the time-of-flight (TOF)
   calculation.  This gives a range value that is considerably more acurate
   and stable than before, in that it is now immune to the crystal frequencey
   offset between the participants.
   
d) Minor tidy of register definitions file for release.
   
=============================================================================
Package v2.03 / Driver v4.0.5  (6th January 2017)
=============================================================================

a) updated the API version number to 4.0.5 

b) Made the local device data structure an array, this is needed when
   multiple devices are used e.g. in the testing platform, test applications
   (testing receiver sensitivity).

c) New dwt_setlocaldataptr API function to set the local pointer to point to
   one of the local device data structure array elements. By default the
   pointer points to 1st element.
   
=============================================================================
Package v2.03 / Driver v4.0.4  (3rd January 2017)
=============================================================================

a) updated the API version number to 4.0.4 

b) Added a missing DWT_LOADEUI SLEEP/WAKEUP parameter

c) updated dwt_otpwriteandverify function definition so it returns the
   correct value type: int

=============================================================================
Package v2.03 / Driver v4.0.3  (1st December 2016)
=============================================================================

a) Added new functions in API to compensate the transmit bandwidth and power
   settings to account for changes caused by temperature variations. See app
   note APS023 part 2 for full details on the rationale and the methodology
   behind this.
   
   - Bandwidth compensation handled by dwt_compensatebandwidth 

   - Power compensation handled by dwt_compensatepower

b) Added a new function to the API, related to point a), that deals with
   calculating a reference measurement (the pulse generator count value)
   needed during calibration: dwt_calculatepgcount

c) Added an internal function _dwt_changepowersetting, which handles
   adjustments to the TX power register setting. Changing this value is a
   little tricky, so this function handles it correctly. It is only used by
   API functions and should not be called.

=============================================================================
Package v2.02 / Driver v4.0.2  (26th October 2016)
=============================================================================

a) Fixed configuration of standard/non-standard SFD.

=============================================================================
Package v2.01 / Driver v4.0.1  (30th June 2016)
=============================================================================

a) Refactored interrupt management in driver:

    - RX callback has been split in three different ones (good frames,
      timeout and errors).

    - "event", "aatset" and "dblbuff" fields in callback data structure have
      been removed as they are duplicates or can be maintained by user
      application.
      
    - Removed support of automatic RX re-enabling for both single buffering
      and double buffering mode.

b) Removed dwt_checkoverrun and dwt_setautorxreenable APIs since automatic RX
   re-enabling is not supported anymore.

c) Added new functions in API to set SNIFF mode (dwt_setsniffmode, replacing
   dwt_setrxmode) and low-power listening mode (dwt_setlowpowerlistening,
   dwt_setsnoozetime and dwt_lowpowerlistenisr).

d) Ranging bit signalling: Added a parameter to dwt_writetxfctrl to provide
   for setting the "ranging" bit in TX frames. This is signaled in the PHY
   Header portion of the physical layer frame. The status of the "ranging"
   bit in RX frames is made available through the rx_flags field of callback
   data structure passed in the RX frame callback called by the dwt_isr()
   interrupt handler. NB: The DW1000 ignores this bit (i.e. it does not have
   to be set to enable TX and RX time-stamping).

e) Added dwt_getinitxtaltrim API to get initial value of XTAL trim configured
   during initialisation.

f) Removed range bias correction tables and API. This was a target platform
   specific implementation assuming LOS and is not a general solution for the
   DW1000.

g) Changed API_ERROR_CHECK scheme to use asserts instead of function returns.

h) Added proper defines to manage dwt_setleds parameter.

i) Added the following options to dwt_rxenable parameter:

    - Make the RX enabling optional in the case that the API is used LATE
      (i.e. start time is deemed to be passed).
      
    - Make IC and host side RX buffer's pointers synchronisation optional.
      This is needed to perform manual RX re-enabling when in double
      buffering mode. This also allows to avoid wasting time doing it if we
      know we are not using double buffering.

j) Added new API functions to allow single byte (8-bit) read/write accesses
   to DW1000 registers (dwt_read8bitoffsetreg and dwt_write8bitoffsetreg).

k) Added new function in API to activate/deactivate fine grain TX sequencing
   (dwt_setfingraintxseq). Using this is necessary for correct operation of
   external PA management.

l) Renamed dwt_setGPIOforEXTTRX API to dwt_setlnapamode and corrected the "PA
   mode" option.

m) Changed the following API names (to make functions' names more consistent
   in the whole driver):
   
    - dwt_xtaltrim to dwt_setxtalrim
    - dwt_setGPIOdirection to dwt_setgpiodirection
    - dwt_setGPIOvalue to dwt_setgpiovalue
    - dwt_checkIRQ to dwt_checkirq

n) Other minor bug fixes in driver.

o) Added the following new examples:
    - 1d: TX with timed sleep
    - 2b: RX using preamble 64
    - 2c: RX with diagnostics
    - 2d: RX using SNIFF mode
    - 2e: RX using double buffering mode
    - 3c: TX then wait for response with GPIOs/LEDs
    - 3d: TX then wait for response using interrutps
    - 7a: Auto ACK TX
    - 7b: Auto ACK RX
    - 8a: Low-power listening RX
    - 8b: Low-power listening TX

p) Added a preamble detect timeout in Double-Sided Two-Way-Ranging examples
   (ex_05a_ds_twr_init and ex_05b_ds_twr_resp).

q) Changed all TWR examples (5a/5b/6a/6b) to correctly handle the LATE
   failure mode of the "delayed TX". Also added an RX reset in case of an
   error occurring in frame reception (e.g. PHY header error, or CRC error,
   etc). This is done to ensure a complete receiver recovery in the event of
   an RX error.

r) Changed all existing examples to handle dwt_initialise returning a failure
   indication.

s) Miscellaneous formatting/comment/typo fixes in driver and examples.

=============================================================================
Package v1.01 / Driver v3.0.1  (3rd December 2015)
=============================================================================

a) Initial release of DW1000 Driver and API unbundled from other
   applications, with some simple example projects.

=============================================================================
