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

-	There are various tasks involved in format conversion

     -	Identify the .csv file and create the variables to access the variables or paths 

-	Check if directories and files mentioned in .csv exists or not

      -	Netlist, output directories need to be verified. As this is User Interface, all user needed information should be listed in detail.

-	Read “Constrains file” for above .csv and convert to SDC format which can be used for synthesis and PNR purpose. 

-	Read all files in “Netlist Directory”

-	Create main synthesis script in format[2]

-	Pass the script to Yosys. 

      - For any errors in here .log file can be referred. 

#### Auto - Create variables using matrix and arrays 
The main benefit of the Auto create is we don’t need need to be depend on the location mentioned in the .csv file. 
It read the file into a matrix form and saves in a matrix ‘m’. Then the matrix ‘m’ can be used to form an array where the information from the .csv file is taken and here, the space between the words is removed. For converting .csv file to array, TCL command **lindex** is used. 
 

The above-described process is shown as an example by taking the 1st values Design Name from the .csv file.
 


Assign filename to the variable `$filename` so, it can be used every time when filename needs to be accessed. 

	`set filename [lindex $argv 0]
	package require csv
	package require struct::matrix
	struct::matrix m
	set f [open $filename]
	csv::read2matrix $f m , auto
	close $f
	set columns [m columns]
	#m add columns $columns
	m link my_arr
	set num_of_rows [m rows]
	set i 0`


By using, above code, a variable is created, it will read the 1st column from .csv and assign the information from 2nd column. This process is direct form Design Name but when .csv file 2nd row is read, it shows, /outputdirectory as path for Output Directory Variable. 

Here, the file location needs to be normalized. For that TCL command `file normalize $my_arr(1, $i)` is used. 
This is needed to have an absolute path rather than a ~ character in the file path.
The outcome of above code:

 ![image](https://github.com/user-attachments/assets/305db919-7e76-48ca-a0b6-25d9696c8499)



### SubTask TWO: From CSV to format[1] and SDC - Processing constraints, CSV

Now, the openMSP430_design_constraints.csv will be read and convert it into a matrix format then convert it into a SDC format. 
Here, there are 3 section of constraints CLOCK, INPUT and OUTPUT. After converting the .csv file into matrix, obtain the number of rows and columns in the .csv file. For this, a complex matrix processing is used.

	`puts "\nInfo: Dumping SDC contraints file for $DesignName \n"
	::struct::matrix constraints
	set  chan [open $ConstraintsFile]
	csv::read2matrix $chan constraints  , auto
	close $chan
	set number_of_rows [constraints rows]
	puts "number_of_rows = $number_of_rows"
	set number_of_columns [constraints columns]
	puts "number_of_columns = $number_of_columns"`



  

Later, obtain row and column for each constrain – CLOCK, INPUT and OUTPUT as well.

**LAB2**

Command to run the `.vsdsynth` from vsdsyth folder (where the file exists) is as below:

openMSP430_desing_details.csv is the input file for vsdsynth tool

`./vsdsynth openMSP430_desing_details.csv`

Variables Creation using TCL


 ![image](https://github.com/user-attachments/assets/5e043efd-a942-4d1d-961f-b33bdf077307)


Later, Remove the OutputDirectory by using the command `rm -rf outdir_openMAP430`


 ![image](https://github.com/user-attachments/assets/9b49b5c4-784c-4ac3-856a-1a5fb0faf558)


Now, when the script is executed again, as the output directory is removed, it should automatically create one. 


![image](https://github.com/user-attachments/assets/3c14ceb8-0c5f-4815-a9ac-62df0262f964)

 

The output directory is created back:


![image](https://github.com/user-attachments/assets/14e8d77b-d306-46c7-b7e7-0420b28ef558)
 

To observe how the error will occur, the Netlist Directory path from .csv file is edited as below. 


![image](https://github.com/user-attachments/assets/af174390-abf8-4d7e-b38f-8bb09229248a)

  

Execute the script again to see below error:

![image](https://github.com/user-attachments/assets/89ad8e20-337e-4bed-b715-966261aad6e0)


Before proceeding to constraints.csv file, verify the directories and .lib files mentioned in the openMSP430_design_details.csv file are available or not. 

 ![image](https://github.com/user-attachments/assets/1749e2a9-8ee8-40d8-9e6a-02261edf01f6)



openMSP430_design_constraints.csv file 

 ![image](https://github.com/user-attachments/assets/9e9094f1-7991-45b6-8a7a-b20168094973)


Output for number of rows and columns of the constraints.csv file

 ![image](https://github.com/user-attachments/assets/2ee45597-3b69-4d05-b192-ab2c7ffe0c08)


Get the row and columns information for all CLOCK, INPUT and OUTPUT

 ![image](https://github.com/user-attachments/assets/e10453a4-f9a4-4d00-9a59-1a18d6ff3d4d)


## 3. Day3 : Processing Clock anf Input Constraints
### SubTask TWO: From CSV to format[1] and SDC - Processing clock constraints

Now, after knowing the row and column of CLOCK, the next step is to identify the column number for clock latency constraints. 

To obtain the information of early_raise_delay, early_fall_delay, late_rise_delay, late_fall_delay and then to convert the clock details in excel to the output format[1], we write an algorithm for it.

To get the columns for all the above constraints, a loop is created between the clock start point to before input start point  and print in desired format.

	`loop while “$clock_start+1”` < “input_ports_start -1” `

After restricting the search space in the excel sheet, obtain the clock details from .csv constraints file.

	`set clock_early_rise_delay_start [lindex [lindex [constraints search rect $clock_start_column $clock_start [expr {$number_of_columns-1}] [expr {$input_ports_start-1}] early_rise_delay] 0] 0]`


![image](https://github.com/user-attachments/assets/7677ba65-2e60-49a4-9a25-a22100402580)


Follow the same process for all clock latency and clock skew constraints of both `dco_clk` and `lfxt_clk` and the put the data into the SDC file.
But the process to obtain the clock period and duty cycle are different. 

	`create_clock -name [constraints get cell 0 $i] -period [constraints get cell 1 $i] -waveform \{0 [expr {[constraints get cell 1 $i]*[constraints get cell 2 $i]/100}]\} \[get_ports [constraints gfet cell 0 $i]\]`

Here, it creates a clock name from column 0 and and takes Period from Column 1, then duty cycle is obtained from column 2. 
As we know the locations from excel sheet, get cell is hard wired to column 0 /1/2. 


`expr` is the expression command that executes in TCL code.

Waveform expression is formed from below part of the code:

	`[expr {[constraints get cell 1 $i]*[constraints get cell 2 $i]/100}]\} `

 
![image](https://github.com/user-attachments/assets/04466540-e124-48d1-8f4c-72cc6b5cfe68)



### SubTask TWO: From CSV to format[1] and SDC - Processing input constraints

The next task after converting CLOCK constraints to SDC format is to convert INPUT constraints into a SDC format. The complication here is, in the INPUTs few are single bits, and most are multi-bits (i.e.) a bus. So, the code needs to be in a way to support n-bit bus.
`*` this indicates that the constraint is a BUS.
Process is to edit the source Verilog file and add ‘*’ to n-bit bus constraints.
The constricted search from constraint .csv file here is from start to end of Inputs section. For the below part of the code is used.

	`set related_clock [lindex [lindex [constraints search rect $clock_start_column $input_ports_start [expr {$number_of_columns-1}] [expr {$output_ports_start-1}] clocks] 0 ] 0]`

After obtaining the restricted search area, 
1.	Get input port.
2.	Open each .v file and grep for $patternt1 in each file.
3.	Split above output string using ‘;’ as delimiter and get lindex[0].
4.	Grep for string “input” in patterns generated by above step
5.	Replace multiple spaces by single space
6.	Count number of elements in this string and save it as $count.
 

![image](https://github.com/user-attachments/assets/002cf28f-0f9d-4f4d-ad47-34fb1dbf2993)


For File access and pattern creation, we use `globing`. It is for searching all .v files in the NetlistDirectory. Then create a new string `$Pattern2`which takes `$Patternt1`and splits and get fixed space string which is saved in a tmp file.


The process to obtain OUTPUTS constraints in SDC format is same as INPUTS constraints. The only change is to modify the restricted search area as per the Output constraints in the constraints .csv file.
 
![image](https://github.com/user-attachments/assets/658ba704-bb66-495d-ba35-f4f6a02feb66)

**LAB3**

Making sure outdir_openMSP430 folder is empty, if any files are present remove them.

![image](https://github.com/user-attachments/assets/5b08342f-26fd-4994-bb23-270b8446e49d)



Clock constraints code output 

![image](https://github.com/user-attachments/assets/ff814d37-58f6-4922-a12a-529c27b71abf)


Run the script with complete clock constraints to SDC format. Now .sdc file is created in outdir_openMSP430 folder.

![image](https://github.com/user-attachments/assets/f2ecd4e4-10fe-4c08-bb42-6dd684dca518)


Open the Constraints file and check if the printed details in the SDC file are same as constraints .csv file information.


![image](https://github.com/user-attachments/assets/f17d6fbb-00fc-45aa-9591-e4d984f011f0)


List of the Verilog files


![image](https://github.com/user-attachments/assets/b1cd1627-620e-48a2-a358-7e5818584f20)


Obtained Pattern1 and Pattern2 for all input ports from all the Verilog files. 


![image](https://github.com/user-attachments/assets/002e5e13-f80f-4bdd-8f6b-00c839f985ea)


Replacing multiple spaces using the `grep` command

![image](https://github.com/user-attachments/assets/8442b1b4-503b-41e7-9c16-c62e6ec66522)


Grepping input ports and fixing spaces

![image](https://github.com/user-attachments/assets/0217890c-f9e0-4405-ad76-9d461bad91a3)

Grepping input ports with fixed spaces


![image](https://github.com/user-attachments/assets/a04c5f5b-8288-460c-be28-5b00167d156d)

Reading Input ports

![image](https://github.com/user-attachments/assets/7cc1cef0-d0b1-4594-ab61-221a485e5018)


Splitting Input ports

![image](https://github.com/user-attachments/assets/418fc028-9cf7-4e3b-9976-65be249d555d)

Uniquify and sort Input ports

![image](https://github.com/user-attachments/assets/a1e53c65-c10f-4489-8318-ea529f3a9070)


Join Input ports

![image](https://github.com/user-attachments/assets/fc39d6ec-bfbe-498f-ba0a-0f2ecab1366d)

Input port count

![image](https://github.com/user-attachments/assets/56e63d4c-47f7-4907-8b65-e87f64856866)

Input port grep to see ‘*’ for busses

![image](https://github.com/user-attachments/assets/0d631885-fd6c-47df-9c1a-ae40e9c67ed8)

All constraints got dumped into SDC file

![image](https://github.com/user-attachments/assets/cb8abcfc-517a-4dcb-8470-1fd34a4a0bd4)


Checking ‘*’ bussed input ports in SDC file

![image](https://github.com/user-attachments/assets/156da82d-21ee-479e-b78b-e7ca7b47f76d)

Re-verifying all bussed ports have ‘*’ 

![image](https://github.com/user-attachments/assets/74de17f5-f1f2-4df7-b7a0-0b8481df51ba)


Output Constraints in SDC format
![image](https://github.com/user-attachments/assets/c587a3ba-f9e7-417f-a604-3ca2213b2aa6)

Output Constraints 

![image](https://github.com/user-attachments/assets/fb599791-5c1a-49fa-99a2-f40d25894589)








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
