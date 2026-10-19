# Getting Started with Efabless FOSS

This guide walks you through using Efabless's `FOSS-ASIC-TOOLS` repository for VLSI course labs. It assumes you have completed the installation guide in that repository and are starting a new environment. Complete Step 0 once to create a local designs folder. Repeat Step 1 whenever you need to launch a new container after closing the main Ubuntu terminal or restarting your PC. The inverter example then walks you through creating and simulating your first circuit in **Xschem**.

## Setup

### Step 0: Create a local designs folder

Open a terminal in Ubuntu and run these commands one at a time. Everything after a `#` is a Bash comment.

```bash
cd "$HOME" # Go to your home folder.
mkdir my-local-design-files # Create the folder if it does not already exist.
```

This folder stores your designs between labs and homework assignments.

### Step 1: Set up and launch the Docker image

From the Ubuntu terminal, run:

```bash
export DESIGNS="$HOME/my-local-design-files"
docker run -it -p 80:80 --user $(id -u):$(id -g) -v "$DESIGNS:/foss/designs" efabless/foss-asic-tools:latest bash
```

The volume mount connects `/foss/designs` inside the container to your local `my-local-design-files` folder. Save your schematics there to keep them after the container stops.

Open a browser, type `http://localhost` into the address bar, and press **Enter**. Enter it as an address rather than a web search; the container hosts the desktop locally on your machine.

![Enter localhost in the browser address bar](./imgs/type_localhost.png)

Enter `abc123` into the password box near the top of the page and press **Enter**.

![Enter the password to access the virtual desktop](./imgs/enter_password.png)

You should now see the virtual desktop with the lab tools available.

## Inverter Example

Follow the numbered steps below to build an inverter, configure its input and supply, and plot its simulated response. The screenshots use automatically assigned component and net names; check your own names as you go.

### 1. Open a terminal in the virtual desktop

Use the terminal launcher inside the browser's virtual desktop. Run the following Xschem command there.

![Open a terminal in the virtual desktop](./imgs/1_open_terminal.png)

### 2. Launch Xschem

In the virtual desktop terminal, run:

```bash
xschem
```

![Launch Xschem from the terminal](./imgs/2_open_xschem.png)

### 3. Maximize the Xschem window

Maximize Xschem so you have room to place components and wire the circuit.

![Maximize the Xschem schematic editor](./imgs/3_fullscreen_xschem.png)

### 4. Show the toolbar

Open the **View** menu and enable **Show toolbar** to display the editing tools shown in the following screenshots.

![Enable the toolbar from the View menu](./imgs/4_show_toolbar.png)

### 5. Save the schematic in /foss/designs

Choose **File → Save As** and save your schematic as `inverter.sch` in `/foss/designs`.

![Save the inverter schematic in the mounted designs folder](./imgs/5_save_schematic_in_foss-designs.png)

### 6. Check the filename and local copy

Confirm that the Xschem title bar shows your schematic's filename.

![Check the saved schematic name in the title bar](./imgs/6_check_name.png)

On your Ubuntu host, open `~/my-local-design-files` and confirm that the schematic appears there. This checks that you saved in the mounted folder.

![Confirm that the schematic appears in the local Ubuntu designs folder](./imgs/6_Check_local_ubuntu_folder.png)

### 7. Insert a symbol

Right-click an empty area of the schematic and choose **Insert symbol** to open the symbol browser.

![Right-click the canvas and choose Insert symbol](./imgs/7_right_click_and_insert_symbol.png)

### 8. Navigate the symbol browser

Click **Current dir**, then **Up**, to browse the folders shown in the screenshot. You will use the PDK symbols for the transistors and the Xschem device symbols for sources and simulation directives.

![Use Current dir and Up to browse symbol folders](./imgs/8_click_current_dir_then_Up_to_see_these_folders.png)

### 9. Open the SKY130 transistor symbol folder

Navigate to the folder shown in the symbol browser:

```text
/foss/pdks/sky130A/libs.tech/xschem/sky130_fd_pr
```

![Navigate to the SKY130 transistor symbol library](./imgs/9_navigate_to_this_folder_for_symbols.png)

### 10. Select the NFET symbol

Select `nfet_05v0_nvt.sym` and click **OK** to attach the symbol to your cursor.

![Select the nfet_05v0_nvt symbol](./imgs/10_select_nfet_05v0_nvt-sym_and_press_ok_to_add_symbol.png)

### 11. Place the transistor symbols

Left-click on the canvas to place the NFET. Repeat the insertion process for `pfet_g5v0d10v5.sym`, placing the PFET above the NFET as shown in the next steps.

![Left-click to place the selected transistor](./imgs/11_left_click_to_place.png)

### 12. Add two voltage sources

Open the symbol browser again and navigate to the Xschem `devices` folder shown in the screenshot. Insert `vsource.sym`, then copy it or insert a second instance. These sources will provide the input signal and the power supply.

![Select vsource from the devices library and add two instances](./imgs/12_add_vsource_symbol_from_this_new_folder_and_copy_a_second_source.png)

### 13. Check the components

Before continuing, confirm that you have one PFET, one NFET, and two voltage sources. Arrange the PFET above the NFET and the sources to their left, matching the screenshot.

![Checkpoint showing two transistors and two voltage sources](./imgs/13_check_point_you_should_have_all_these_symbols.png)

### 14. Set the supply voltage

Double-click the supply source, labeled `V2` in this example. Change its `value` to `5` and click **OK** to set a 5 V DC supply.

![Set the V2 supply source value to 5 volts](./imgs/14_double_click_on_V2_and_edit_value_to_5_press_ok.png)

### 15. Configure the input pulse

Double-click the input source, labeled `V1` in this example, and set its value to:

```text
PULSE(0 5 1n 1n 1n 4n 10n)
```

This switches the input between 0 V and 5 V, with a 1 ns initial delay, 1 ns rise and fall times, a 4 ns pulse width, and a 10 ns period. Click **OK** to apply it.

![Configure V1 as a pulse source](./imgs/15_make_V1_a_pulse.png)

### 16. Add ground

Insert `gnd.sym` from the Xschem `devices` library and place it below the circuit. It will connect to the common return wire.

![Place a ground symbol below the inverter](./imgs/16_add_gnd.png)

### 17. Wire the inverter

Use the **W** key to start wires at component pins and connect the circuit as shown:

- Connect the PFET source and bulk to the positive terminal of the 5 V supply.
- Connect the NFET source and bulk, both voltage sources' negative terminals, and ground together.
- Connect both transistor gates to the positive terminal of the pulse source. This is the input node.
- Connect the two transistor drains together. This is the output node.

Check that wires end on the intended pins and that the junctions match the screenshot.

![Wire the inverter, voltage sources, and ground](./imgs/17_add_wires_with_the_w_key.png)

### 18. Increase the PFET width

Double-click the PFET and set its width parameter `W` to three times the NFET's width. In the screenshots, the NFET width is `1` and the PFET width is changed to `3`. Keep the other device properties as shown and click **OK**.

![Edit the PFET width to three times the NFET width](./imgs/18_edit_pfet_to_three_times_the_width_of_nfet.png)

### 19. Record the input and output net names

Double-click the input and output wires to inspect their net names. Write them down for the plotting command later. The example uses `net1` for the input and `net2` for the output; your automatically generated names may differ.

![Inspect the wire properties to check net names](./imgs/19_double_click_wires_to_cehck_net_name.png)

### 20. Add a code symbol

Insert `code.sym` from the Xschem `devices` library and place it beside the circuit. This symbol holds the model-library and simulation directives.

![Insert a code symbol beside the inverter](./imgs/20_add_code_symbol.png)

### 21. Enter the simulation directives

Double-click the code symbol and enter the following text in its `value` field, preserving the symbol's existing name and other properties:

```spice
.lib /foss/pdks/sky130A/libs.tech/ngspice/sky130.lib.spice tt
.tran 0.1n 100n
.save all
```

The `.lib` line loads the SKY130 models at the typical (`tt`) process corner. The `.tran` line requests a transient simulation with a 0.1 ns output interval and a 100 ns stop time. The `.save all` line saves signals for plotting. Click **OK**.

![Enter the SKY130 model and transient simulation directives](./imgs/21_copy_code_below_into_code_symbol.png)

### 22. Save your work

Save the schematic again so the component values, wiring, and simulation directives are stored in your designs folder.

![Save the completed inverter schematic](./imgs/22_save.png)

### 23. Generate the netlist

Click **Netlist** in the upper-right corner of Xschem to generate the SPICE representation of your schematic.

![Click Netlist to generate the SPICE netlist](./imgs/23_press_netlist_button.png)

### 24. Run the simulation

Click **Simulate** beside **Netlist**. An ngspice terminal should open and complete the transient analysis, leaving an `ngspice` prompt. Check for errors before proceeding; if any appear, recheck the wiring, component values, and model-library path.

![Check the ngspice terminal after running the simulation](./imgs/24_you_should_get_a_spice_termial_with_no_errors.png)

### 25. Plot the input and output

At the ngspice prompt, enter the command below, replacing `net1` and `net2` with the input and output net names you recorded in step 19:

```text
plot net1 net2
```

![Plot the input and output using your circuit's net names](./imgs/25_run_plot_with_your_specific_input_and_ouput_net_names.png)

### 26. Check the inverter response

The plot should show the output going low when the input is high and going high when the input is low, with transitions around the input edges. Compare your waveforms with the screenshot to confirm that your circuit behaves as an inverter.

![Completed inverter simulation with input and output waveforms](./imgs/26_congrats_youve_made_an_inverter.png)
