# Experiment 4: Implement Software Defined Networking (SDN) Using Mininet, Open vSwitch, and Ryu Controller

## Aim
To create and configure a Software Defined Networking (SDN) environment using Mininet, Open vSwitch, and the Ryu Controller. The experiment demonstrates basic network connectivity, traffic forwarding, and packet filtering using OpenFlow rules.

## Technologies and Tools Used
- **Operating System:** Ubuntu Linux
- **Network Emulator:** Mininet
- **SDN Switch:** Open vSwitch (OVS)
- **SDN Controller:** Ryu Controller
- **Programming Language:** Python
- **Protocol:** OpenFlow 1.3
- **Terminal:** Ubuntu Terminal

## Experiment Overview
Software Defined Networking (SDN) is a networking approach that separates the control plane from the data plane.

- **Mininet:** Creates virtual hosts, switches, and network topologies.
- **Open vSwitch:** Acts as a software-based network switch.
- **Ryu Controller:** Controls network devices and manages traffic forwarding.
- **OpenFlow:** Allows the controller to communicate with switches and install flow rules.

In this experiment, a network containing three virtual hosts connected to one Open vSwitch is created. The Ryu Controller manages the switch, and OpenFlow rules are used to demonstrate traffic filtering.

---

## Task 1: Update Ubuntu System

Update and upgrade the Ubuntu system.

```bash
sudo apt update && sudo apt upgrade -y
```

**Purpose:** Updates the package list and upgrades installed packages.

## Task 2: Install Mininet

Install Mininet for creating virtual networks.

```bash
sudo apt install mininet -y
```

**Purpose:** Installs Mininet, which is used to create virtual hosts and switches.

## Task 3: Install Open vSwitch

Install the Open vSwitch software.

```bash
sudo apt install openvswitch-switch -y
```

Check its status:

```bash
sudo systemctl status openvswitch-switch
```

**Purpose:** Installs and checks the software-based SDN switch.

## Task 4: Test Mininet Installation

Start a basic Mininet network.

```bash
sudo mn
```

Execute the following commands inside the Mininet CLI:

```bash
nodes
pingall
exit
```

**Purpose:**
- `nodes` displays the available virtual hosts and switch.
- `pingall` tests connectivity between virtual hosts.
- `exit` closes Mininet.

## Task 5: Install Python and Virtual Environment

Install Python package and virtual-environment support.

```bash
sudo apt install python3-pip python3-venv -y
```

**Purpose:** Prepares the system for installing and running the Ryu Controller.

## Task 6: Create a Python Environment for Ryu

Create and activate a virtual environment.

```bash
python3 -m venv ryu-env
source ryu-env/bin/activate
```

**Purpose:** Creates an isolated Python environment for Ryu and its dependencies.

## Task 7: Install Ryu Controller

Install the Ryu SDN Controller.

```bash
pip install ryu
```

Check the installation:

```bash
ryu-manager --version
```

**Purpose:** Installs Ryu and verifies its version.

**Note:** If the installation fails due to Python compatibility or dependency errors, use a compatible Python environment rather than assuming the installation was successful.

## Task 8: Start the Ryu Controller

Start Ryu with the Simple Switch OpenFlow 1.3 application.

```bash
ryu-manager ryu.app.simple_switch_13
```

**Purpose:** Starts the Ryu Controller, which manages network traffic using OpenFlow 1.3.

**Important:** Keep this terminal open while performing the remaining SDN tasks.

## Task 9: Create Mininet SDN Topology

Open a new terminal and execute:

```bash
sudo mn --topo single,3 --controller=remote --switch ovsk
```

**Purpose:** Creates an SDN topology containing:
- Three virtual hosts: H1, H2, and H3
- One Open vSwitch: S1
- One remote controller: Ryu

## Task 10: Check Network Nodes

Inside the Mininet CLI, execute:

```bash
nodes
```

**Expected Output:** The network should display H1, H2, H3, and S1.

**Purpose:** Verifies that the virtual hosts and switch have been created.

## Task 11: Check Network Connections

Execute:

```bash
net
```

**Purpose:** Displays the connections between the virtual hosts and the Open vSwitch.

## Task 12: Test Network Connectivity

Test connectivity between all hosts:

```bash
pingall
```

Test connectivity between H1 and H2:

```bash
h1 ping h2
```

**Purpose:** Verifies communication between the virtual hosts.

## Task 13: View OpenFlow Rules

Open another terminal and execute:

```bash
sudo ovs-ofctl -O OpenFlow13 dump-flows s1
```

**Purpose:** Displays the flow rules installed in the Open vSwitch.

## Task 14: Add a Simple Filtering Rule

Add a flow rule to drop packets entering S1 through port 1.

```bash
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "priority=100,in_port=1,actions=drop"
```

**Purpose:** Demonstrates traffic filtering using an OpenFlow rule.

## Task 15: Test the Filtering Rule

Return to the Mininet terminal and execute:

```bash
h1 ping h2
```

**Purpose:** Checks the effect of the filtering rule on traffic from H1.

The rule drops packets entering switch S1 through port 1. Therefore, traffic from the host connected to that port should be affected.

## Task 16: Remove Flow Rules

Remove the flow rules from the switch.

```bash
sudo ovs-ofctl -O OpenFlow13 del-flows s1
```

**Purpose:** Deletes the manually added flow rules.

## Task 17: Clean Mininet Environment

Execute the following command after completing the experiment:

```bash
sudo mn -c
```

**Purpose:** Cleans up Mininet interfaces, processes, and temporary network configurations.

---

## Expected Results

After completing the experiment:

1. Ubuntu is updated successfully.
2. Mininet and Open vSwitch are installed.
3. The Ryu Controller is installed and started.
4. A network topology with three hosts and one switch is created.
5. Network connectivity is tested using `pingall` and `h1 ping h2`.
6. OpenFlow rules are viewed and configured.
7. Packet filtering is demonstrated using a drop rule.
8. The Mininet environment is cleaned up.

## Screenshots to Include

Add the following screenshots to your GitHub repository as evidence of execution:

1. Ubuntu update and upgrade.
2. Mininet and Open vSwitch installation.
3. Open vSwitch service status.
4. Basic Mininet network using `sudo mn`.
5. Ryu Controller running successfully.
6. Mininet topology showing H1, H2, H3, and S1.
7. Output of the `nodes` and `net` commands.
8. Successful `pingall` connectivity test.
9. OpenFlow rules using `dump-flows`.
10. Filtering rule added to Open vSwitch.
11. Ping test after applying the filtering rule.
12. Mininet cleanup.

## Learning Outcomes

After completing this experiment, I learned:

- The basic concepts of Software Defined Networking (SDN).
- How to create virtual networks using Mininet.
- How Open vSwitch works as a software-based switch.
- How to install and run the Ryu SDN Controller.
- How the OpenFlow protocol manages network traffic.
- How to create and test network topologies.
- How to add and remove flow rules.
- How to demonstrate packet filtering in an SDN environment.

## Precautions

- Keep the Ryu Controller running while testing the remote Mininet topology.
- Run Mininet and Open vSwitch commands with appropriate permissions.
- Use the correct Python virtual environment when running Ryu.
- Ensure Open vSwitch is running before creating the topology.
- Verify network connectivity before applying filtering rules.
- Clean up the Mininet environment after completing the experiment.

## Conclusion

The SDN environment was created using Mininet, Open vSwitch, and the Ryu Controller. A network topology with three virtual hosts and one switch was configured. Network connectivity was tested, and OpenFlow rules were used to demonstrate basic traffic forwarding and packet filtering. Thus, the experiment provided practical knowledge of Software Defined Networking and centralized network control.
