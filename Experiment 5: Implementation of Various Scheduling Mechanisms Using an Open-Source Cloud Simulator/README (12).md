# Experiment 5: Implementation of Various Scheduling Mechanisms Using an Open-Source Cloud Simulator

## Aim

To implement and compare different Virtual Machine (VM) scheduling and Cloudlet scheduling mechanisms using the CloudSim Plus cloud simulator.

## Technologies and Tools Used

- **Operating System:** Ubuntu Linux
- **Programming Language:** Java 17
- **Cloud Simulator:** CloudSim Plus 8.5.5
- **Build Tool:** Apache Maven
- **Version Control:** Git
- **IDE / Editor:** Nano / VS Code
- **Simulation Framework:** CloudSim Plus

## Experiment Overview

Cloud scheduling is the process of allocating cloud resources to Virtual Machines (VMs) and assigning tasks to those VMs for execution.

This experiment uses CloudSim Plus to implement and compare four scheduling combinations:

1. VM SpaceShared + Cloudlet SpaceShared
2. VM SpaceShared + Cloudlet TimeShared
3. VM TimeShared + Cloudlet SpaceShared
4. VM TimeShared + Cloudlet TimeShared

**Types of Scheduling:**

- **SpaceShared Scheduling:** A resource is allocated exclusively to one task or VM at a time until its execution is completed.
- **TimeShared Scheduling:** Available processing resources are shared among multiple tasks or VMs using time allocation.

The simulation displays Cloudlet execution details, including VM ID, status, start time, and finish time.

---

## Task 1: Update Ubuntu System

Update the Ubuntu package index:

```bash
sudo apt update
```

Upgrade installed packages:

```bash
sudo apt upgrade -y
```

**Purpose:** Updates the Ubuntu package list and installs available software updates.

## Task 2: Install Java, Maven, and Git

Install the required tools:

```bash
sudo apt install openjdk-17-jdk maven git -y
```

Verify Java installation:

```bash
java -version
```

Verify Java compiler:

```bash
javac -version
```

Verify Maven installation:

```bash
mvn -version
```

Verify Git installation:

```bash
git --version
```

**Purpose:**
- Java provides the runtime environment.
- Javac compiles Java programs.
- Maven manages project dependencies and builds.
- Git provides version control.

**Expected Result:** Java and javac should report version 17.x, and Maven should use Java 17.

## Task 3: Create the CloudSim Plus Maven Project

### Step 3.1: Create Project Directories

Create the project directory and Java source folder:

```bash
mkdir -p ~/CloudSchedulingLab/src/main/java/lab
```

Navigate to the project directory:

```bash
cd ~/CloudSchedulingLab
```

**Purpose:** Creates the standard Maven project structure.

### Step 3.2: Create the pom.xml File

Open the Maven configuration file:

```bash
nano pom.xml
```

Add the following configuration:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
 https://maven.apache.org/xsd/maven-4.0.0.xsd">

 <modelVersion>4.0.0</modelVersion>

 <groupId>lab</groupId>
 <artifactId>cloud-scheduling-lab</artifactId>
 <version>1.0-SNAPSHOT</version>

 <properties>
  <maven.compiler.release>17</maven.compiler.release>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
 </properties>

 <dependencies>
  <dependency>
   <groupId>org.cloudsimplus</groupId>
   <artifactId>cloudsimplus</artifactId>
   <version>8.5.5</version>
  </dependency>
 </dependencies>

</project>
```

Save the file using:

- `Ctrl + O` → Press Enter
- `Ctrl + X` → Exit Nano

**Purpose:** The `pom.xml` file defines the Maven project, Java version, and CloudSim Plus dependency.

### Step 3.3: Resolve Dependencies

Download the required dependencies:

```bash
mvn dependency:resolve
```

Clean the project:

```bash
mvn clean
```

**Purpose:** Downloads CloudSim Plus libraries and removes previous build output.

## Task 4: Implement the Scheduling Experiment

Create the Java source file:

```bash
nano src/main/java/lab/SchedulingExperiment.java
```

Add the following Java program:

```java
package lab;

import java.util.ArrayList;
import java.util.List;

import org.cloudbus.cloudsim.core.CloudSim;
import org.cloudbus.cloudsim.datacenters.Datacenter;
import org.cloudbus.cloudsim.datacenters.DatacenterSimple;
import org.cloudbus.cloudsim.brokers.DatacenterBroker;
import org.cloudbus.cloudsim.brokers.DatacenterBrokerSimple;
import org.cloudbus.cloudsim.cloudlets.Cloudlet;
import org.cloudbus.cloudsim.cloudlets.CloudletSimple;
import org.cloudbus.cloudsim.hosts.Host;
import org.cloudbus.cloudsim.hosts.HostSimple;
import org.cloudbus.cloudsim.resources.Pe;
import org.cloudbus.cloudsim.resources.PeSimple;
import org.cloudbus.cloudsim.schedulers.cloudlet.CloudletScheduler;
import org.cloudbus.cloudsim.schedulers.cloudlet.CloudletSchedulerSpaceShared;
import org.cloudbus.cloudsim.schedulers.cloudlet.CloudletSchedulerTimeShared;
import org.cloudbus.cloudsim.schedulers.vm.VmScheduler;
import org.cloudbus.cloudsim.schedulers.vm.VmSchedulerSpaceShared;
import org.cloudbus.cloudsim.schedulers.vm.VmSchedulerTimeShared;
import org.cloudbus.cloudsim.utilizationmodels.UtilizationModel;
import org.cloudbus.cloudsim.utilizationmodels.UtilizationModelFull;
import org.cloudbus.cloudsim.vms.Vm;
import org.cloudbus.cloudsim.vms.VmSimple;
import org.cloudbus.cloudsim.provisioners.ResourceProvisionerSimple;
import org.cloudbus.cloudsim.allocationpolicies.VmAllocationPolicySimple;

public class SchedulingExperiment {

    private static final int HOST_PES = 4;
    private static final int VM_COUNT = 2;
    private static final int CLOUDLET_COUNT = 4;
    private static final long MIPS = 1000;
    private static final long CLOUDLET_LENGTH = 20000;

    public static void main(String[] args) {

        runScenario(
            "A: VM SpaceShared + Cloudlet SpaceShared",
            new VmSchedulerSpaceShared(),
            new CloudletSchedulerSpaceShared()
        );

        runScenario(
            "B: VM SpaceShared + Cloudlet TimeShared",
            new VmSchedulerSpaceShared(),
            new CloudletSchedulerTimeShared()
        );

        runScenario(
            "C: VM TimeShared + Cloudlet SpaceShared",
            new VmSchedulerTimeShared(),
            new CloudletSchedulerSpaceShared()
        );

        runScenario(
            "D: VM TimeShared + Cloudlet TimeShared",
            new VmSchedulerTimeShared(),
            new CloudletSchedulerTimeShared()
        );
    }

    private static void runScenario(
        String title,
        VmScheduler vmScheduler,
        CloudletScheduler cloudletScheduler
    ) {

        System.out.println(
            "\n=================================================="
        );
        System.out.println(title);
        System.out.println(
            "=================================================="
        );

        CloudSim simulation = new CloudSim();

        createDatacenter(simulation, vmScheduler);

        DatacenterBroker broker =
            new DatacenterBrokerSimple(simulation);

        List<Vm> vmList = createVms(cloudletScheduler);
        List<Cloudlet> cloudletList = createCloudlets();

        broker.submitVmList(vmList);
        broker.submitCloudletList(cloudletList);

        simulation.start();

        List<Cloudlet> finished =
            broker.getCloudletFinishedList();

        System.out.printf(
            "%-10s %-10s %-12s %-12s %-12s%n",
            "Cloudlet", "VM", "Status", "Start", "Finish"
        );

        for (Cloudlet cloudlet : finished) {

            System.out.printf(
                "%-10d %-10d %-12s %-12.2f %-12.2f%n",
                cloudlet.getId(),
                cloudlet.getVm().getId(),
                cloudlet.getStatus(),
                cloudlet.getExecStartTime(),
                cloudlet.getFinishTime()
            );
        }

        System.out.println(
            "Completed Cloudlets: " + finished.size()
            + " / " + CLOUDLET_COUNT
        );
    }

    private static Datacenter createDatacenter(
        CloudSim simulation,
        VmScheduler vmScheduler
    ) {

        List<Pe> peList = new ArrayList<>();

        for (int i = 0; i < HOST_PES; i++) {
            peList.add(new PeSimple(MIPS));
        }

        Host host = new HostSimple(
            8192, 100000, 1000000, peList
        );

        host.setRamProvisioner(
            new ResourceProvisionerSimple()
        );

        host.setBwProvisioner(
            new ResourceProvisionerSimple()
        );

        host.setVmScheduler(vmScheduler);

        List<Host> hostList = new ArrayList<>();
        hostList.add(host);

        return new DatacenterSimple(
            simulation,
            hostList,
            new VmAllocationPolicySimple()
        );
    }

    private static List<Vm> createVms(
        CloudletScheduler scheduler
    ) {

        List<Vm> vmList = new ArrayList<>();

        for (int i = 0; i < VM_COUNT; i++) {

            Vm vm = new VmSimple(i, MIPS, 2);

            vm.setRam(2048)
              .setBw(10000)
              .setSize(10000);

            vm.setCloudletScheduler(scheduler);

            vmList.add(vm);
        }

        return vmList;
    }

    private static List<Cloudlet> createCloudlets() {

        List<Cloudlet> cloudletList = new ArrayList<>();

        UtilizationModel utilization =
            new UtilizationModelFull();

        for (int i = 0; i < CLOUDLET_COUNT; i++) {

            Cloudlet cloudlet = new CloudletSimple(
                CLOUDLET_LENGTH, 1, utilization
            );

            cloudlet.setSizes(300);

            cloudletList.add(cloudlet);
        }

        return cloudletList;
    }
}
```

Save the file using:

- `Ctrl + O` → Press Enter
- `Ctrl + X` → Exit Nano

**Purpose:** The Java program creates a cloud datacenter, VMs, and Cloudlets, and runs four scheduling scenarios using different VM and Cloudlet schedulers.

### Understanding the Program

| Component | Description |
|---|---|
| CloudSim | Creates and runs the cloud simulation |
| Datacenter | Represents the cloud infrastructure |
| Host | Provides physical computing resources |
| VM | Represents a virtual machine |
| Cloudlet | Represents a task submitted for execution |
| DatacenterBroker | Manages VM and Cloudlet submissions |
| VmScheduler | Controls CPU allocation among VMs |
| CloudletScheduler | Controls CPU allocation among Cloudlets |

## Task 5: Compile and Execute the Program

### Step 5.1: Compile the Project

Execute:

```bash
mvn clean compile
```

**Purpose:** Compiles the Java source code and checks for syntax and API errors.

### Step 5.2: Run the Scheduling Experiment

Execute:

```bash
mvn exec:java -Dexec.mainClass="lab.SchedulingExperiment" -Dexec.classpathScope=compile
```

**Purpose:** Executes the Java program using Maven and the CloudSim Plus dependencies.

If Maven cannot resolve the Exec plugin prefix, use the fully qualified command:

```bash
mvn org.codehaus.mojo:exec-maven-plugin:3.5.0:java \
-Dexec.mainClass="lab.SchedulingExperiment" \
-Dexec.classpathScope=compile
```

**Purpose:** Runs the experiment using a specific version of the Maven Exec plugin.

## Task 6: Observe Simulation Results

After successful execution, the program displays the results of all four scheduling scenarios.

The output contains:

- Cloudlet ID
- VM ID
- Cloudlet status
- Execution start time
- Execution finish time
- Total number of completed Cloudlets

**Purpose:** Helps compare how different VM and Cloudlet scheduling combinations affect task execution.

## Scheduling Mechanisms Comparison

| Scenario | VM Scheduler | Cloudlet Scheduler |
|---|---|---|
| A | SpaceShared | SpaceShared |
| B | SpaceShared | TimeShared |
| C | TimeShared | SpaceShared |
| D | TimeShared | TimeShared |

## Expected Results

After successfully completing the experiment:

1. Ubuntu is updated.
2. Java 17, Maven, and Git are installed.
3. A CloudSim Plus Maven project is created.
4. CloudSim Plus dependencies are resolved.
5. The Java scheduling program is compiled.
6. Four scheduling combinations are executed.
7. Cloudlet execution details are displayed for comparison.

## Screenshots to Include

Add the following screenshots to your GitHub repository:

1. Ubuntu update and upgrade.
2. Java, javac, Maven, and Git version verification.
3. CloudSchedulingLab project directory.
4. `pom.xml` configuration.
5. Successful Maven dependency resolution.
6. Java source code in `SchedulingExperiment.java`.
7. Successful `mvn clean compile`.
8. Execution of the scheduling program.
9. Output of Scenario A.
10. Output of Scenario B.
11. Output of Scenario C.
12. Output of Scenario D.

## Learning Outcomes

After completing this experiment, I learned:

- The basic concepts of cloud scheduling.
- How to install and configure CloudSim Plus.
- How to create a Maven-based Java project.
- How to simulate a cloud datacenter and physical host.
- How to create and configure Virtual Machines.
- How to create Cloudlets for task execution.
- The difference between SpaceShared and TimeShared scheduling.
- How to compare different scheduling mechanisms through simulation.

## Precautions

- Ensure Java 17 is installed and configured correctly.
- Verify that Maven is
