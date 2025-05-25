# TCL-Workshop
VSD - TCL Workshop: Introduction to Advance Skills 
# VSD - Tool Command Language (TCL) Workshop 
This repository contains all the information included in the VSD TCL Workshop. This workshop helped me in learning the TCL scripting and how we to extract the information from CSV file anf the how to create and use a tool box to obtain the pre-layout timing resutls in the desired format. We use Yosys tool for synthesis and OpenTimer tool for pre-layout timing purpose.

## Table of Content:
 1. Day1 : Introduction to TCL and VSDSYNTH Toolbox Usage
 2. Day2 : Variable Creation and Processing Constraints from CSV
 3. Day3 : Processing Clock anf Input Constraints
 4. Day4 : Complete Scripting and YOSYS Synthesis Introduction
 5. Day5 : Advanced Scripting Techniques and Quality of Results Generation
 6. Acknowledgements


## 1. Day1 : Introduction to TCL and VSDSYNTH Toolbox Usage
### Introduction
#### Introduction to TCL and VSDSYNTH Toolbox Usage
 The main task of this workshop is to create a user interface (UI) to use excel file and create the output file 
 Here UI is the TCL box, which is `vsdsynth` for this workshop.
![image](https://github.com/user-attachments/assets/aea59c0f-7348-4d05-9f44-5e14ca9d5bd9)

#### Introduction to Sub-Task
Here, the task is divided into sub-tasks and tools needed are defined.
As the input and desired output format are defined, The Top-Down approach is followed.
  
   **Step1:** Create a demo called .vsdsynth and pass .csv from UNIX shell to TCL script.  
   
   **Step2:** Convert all inputs to format[1] and SDC (Synopsis Design Constraint) format, and pass to synthesis tool 'Yosys'.
   
   **Step3:** Convert format[1] and SDC to format[2] and pass to timing tool 'OpenTimer'.
   
   **Step4:** Generate output report
   
   Here format[1] is the way 'Yosys' needs / accepts for synthesis process as yosys tool can not understand the csv file and where as format[2] is a way 'OpenTimer' needs the information to process Timing Analysis.

### SubTask ONE: VSDSYNTH Toolbox Usage scenarios 

(**Step1:** Create a demo called .vsdsynth and pass .csv from UNIX shell to TCL script.  )

The first take is to create a `vsdsyth`, which will be the user interface tool. As this is UI, it needs to have all the information a User needs to use this TCL tool.
To understand it in detail, it is seen in 3 different user scenarios.
1.	When .csv file is not provided as an input.
In this case, the user interface should understand the .csv file is missing and it should display an Error message “Please provide the .csv file”
2.	User provided a .csv file, which doesn’t exist.
In this case, the .csv file is there but it might not be in the current working directory. The user interface should be able to understand it and display an Error message “Cannot find the .csv file”
3.	Type “-help” to find out usage of the tool
When User want to find the usage of the tool `vsdsynth` -help command should display the Usage information.

Below is the .csv file as an Input
![Excel sheet](https://github.com/user-attachments/assets/19db2e03-c96b-4739-bb3d-08329c8e94a9)

**LAB1**

To run the `vsdsynth`

Go to the directory where the file is there and in shell type `./vsdsynth`

To run the `vsdsynth` permission is required. `chom -R 777 vsdsynth` is the command to give permission to execute `vsdsynth` code.

In *scenario1* when no .cvs file is provided:
![Day1 - vsdsynth csv file not provided](https://github.com/user-attachments/assets/32bdaa97-46a0-4e40-a83b-f3af18dca460)
 

In *scenario2* when .csv file is provided doesn’t exist:
 ![vsdsynth non existing csv file](https://github.com/user-attachments/assets/2de06eaf-aae3-4108-a457-c9227cbce6bb)

In *scenario3* -help command to see user guide
 ![vsdsynth help command](https://github.com/user-attachments/assets/a22da9f4-26d3-456d-b96c-f9d623f9e01c)


## 2. Day2 : Variable Creation and Processing Constraints from CSV

(**Step2:** Convert all inputs to format[1] and SDC (Synopsis Design Constraint) format, and pass to synthesis tool 'Yosys'.)

### SubTask TWO: From CSV to format[1] and SDC - variable creation



### SubTask TWO: From CSV to format[1] and SDC - Processing constraints, CSV



## 3. Day3 : Processing Clock anf Input Constraints
### SubTask TWO: From CSV to format[1] and SDC - Preocessing clock constraints

### SubTask TWO: From CSV to format[1] and SDC - Preocessing input constraints

## 4. Day4 : Complete Scripting and YOSYS Synthesis Introduction
### Full Script download and Conclusion

### Introduction to YOSYS synthesis tool usage

### Hierarchy check and error handling script creation for YOSYS

## 5. Day5 : Advanced Scripting Techniques and Quality of Results Generation
### Synthesis main file scripting and output file editing


( **Step3:** Convert format[1] and SDC to format[2] and pass to timing tool 'OpenTimer'.)

### World of 'Procs'

### read_sdc_proc - interpret clock generation constraints

### read_sdc_proc - interpret IO delay and transition constraints

### Process bussed ports and configuration file creation

### Quality of Results (QOR) generation algorithm

(**Step4:** Generate output report)


### Conclusion
 - The excel sheet is taken and processes using TCL book `vsdsynth` and obtained output in the desired format.
 - All tasks / modules are simulated and any errors seen while processing are resolved.
## 6. Acknowledgements
 - Kunal Ghosh, Co-founder (VSD Corp. Pvt. Ltd)
