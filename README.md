# Bumo-contract-Audit-report [ Full report link ]



**Introduction**

This audit report highlights the overall security of the BU-ERC20 smart contract. With this report, I have tried to ensure the reliability of the smart contract by completing the assessment of their system’s architecture and smart contract codebase. 

**Auditing approach and Methodologies applied**

In this audit, I consider the following crucial features of the code.



*    Whether the implementation of ERC 20 standards.
*    Whether the code is secure.
*    Whether the code meets the best coding practices.
*    Whether the code meets the SWC Registry issue.

The audit has been performed according to the following procedure:

• **Manual audit**



1. Inspecting the code line by line and revert the initial algorithms of the protocol and then

    compare them with the specification

2. Manually analyzing the code for security vulnerabilities.
3. Assessing the overall project structure, complexity & quality.
4. Checking SWC Registry issues in the code. 
5. Unit testing by writing custom unit testing for each function.
6. Checking whether all the libraries used in the code of the latest version.
7. Analysis of security on-chain data. 
8. Analysis of the failure preparations to check how the smart contract performs in case of bugs and vulnerability. 

 • **Automated analysis**



1. Scanning the project's code base with [Mythril](https://github.com/ConsenSys/mythril), [Slither](https://github.com/trailofbits/slither), [Echidna](https://github.com/trailofbits/echidna) , [Manticore](https://github.com/trailofbits/manticore) , [SmartCheck](https://tool.smartdec.net)
2. Manually verifying (reject or confirm) all the issues found by tools.
3. Performing Unit testing.
4. Manual Security Testing (SWC-Registry, Overflow)
5. Running the tests and checking their coverage.

** Report: **All the gathered information is described in this report.

**Audit details**

**Project Name: **BUMO

**Token symbol: **BU (BUMO)

**Language:** Solidity

**Platform and tools:** Remix, VScode, securify and other tools mentioned in the automated analysis section. 

**Audit Goals**

The focus of this audit was to verify whether the smart contract is secure, resilient, and working according to ERC20 specs. The audit activity can be grouped in three categories. 

**Security:** Identifying the security-related issue within each contract and system of contracts.

**Sound architecture:** Evaluating the architect of a system through the lens of established  smart contract best practice and general software practice.  

**Code correctness and quality: **A  full review of contract source code. The primary area of focus includes.



*   Correctness.
*   Section of code with high complexity.
*   Readability.
*   Quantity and quality of test coverage. 

**Security**

Every issue in this report was assigned a severity level from the following:

**High severity issues **

Issues mentioned here are critical to smart contract performance and functionality and should be fixed before moving to mainnet.

**Medium severity issues **

This could potentially bring the problem in the future and should be fixed.

**Low severity issues **

These are minor details and warnings that can remain unfixed but would be better if it got fixed in the future.




** **

**Manual audit**

Following are the report from our manual analysis


## **SWC Registry test**

We have tested some known SWC registry issues. Out of all tests only SWC 102 and 103. Both are low priority.

 
 

**High severity issues**

No High Severity Issue found.

**Medium severity issues**

No Medium Severity Issue found.


## **Low Severity Issues :**

There were 2 low severity issues found.



1. **Using the approve function of the ERC-20 token standard [ Line 539-542 ] **

        The approve function of ERC-20 is vulnerable. Using a front-running attack one can spend approved tokens before the change of allowance value.


        To prevent attack vectors described above, clients should make sure to create user interfaces in such a way that they set the allowance first to 0 before setting it to another value for the same spender. Though the contract itself shouldn't enforce it, to allow backward compatibility with contracts deployed before. 


      Detailed reading around it can be found at[ EIP 20](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md#approve) 


 



2. **Prefer external to public visibility level [ line 220-222 , 248-252, 469-471, 477-479, 501-503, 508-510, 520-523, 528-530, 539-542, 557-561,  575-578, 594-597, 604-609, 622-629, 636-639]  **[Link](https://medium.com/@gus_tavo_guim/public-vs-external-functions-in-solidity-b46bcf0ba3ac)

  function owner() public view returns (address) {

       		 return _owner;

 	   }


    A function with a **public** visibility modifier that is not called internally. Changing the visibility level to **external** increases code readability. Moreover, in many cases, functions with **external** visibility modifiers spend less gas compared to functions with **public** visibility modifiers.


    **Recommendations: **Use the **external** visibility modifier for functions never called from the contract via internal call.


    **Note:** Exact same issue was found while using automated testing by smartcheck. 

**Automated test :**

We have used multiple automated testing frameworks. This makes code more secure common attacks. The results are below.

**Smart Check:**

SmartCheck automatically checks Smart Contracts for vulnerabilities and bad practices. Automated tests have been conducted and got the following report. A total of five errors were found. Out of five, two have already been covered above.

[https://tool.smartdec.net/scan/81bee80fdc7d49b6ba2ac2baff25fdf9](https://tool.smartdec.net/scan/81bee80fdc7d49b6ba2ac2baff25fdf9)









**Error 1: Using approve function of the ERC-20 token standard : **

Already discussed in the manual testing section.

**Error 2: Private modifier**

8 times this error has been reported. [Line 448, 441, 275, 443, 449, 447, 445, 205 ]  

We need to be careful while using a private modifier.  The private modifier does not make a variable invisible. Miners have access to all contracts’ code and data. Developers must account for the lack of privacy in Ethereum. Reference: [Link](https://solidity.readthedocs.io/en/develop/contracts.html#visibility-and-getters)

** **

**Error 3:Use of SafeMath**

Smart Check doesn't recommend using SafeMath library for all arithmetic operations. According to them,  It’s good practice to use explicit checks where it is really needed and to avoid extra checks where overflow/underflow is impossible. 

**Error 4: Prefer external to public visibility level : **

Already discussed in the manual testing section.

**Error 5: Implicit visibility level **

The default function visibility level in contracts is **_public_**, in interfaces - **_external_**, state variable default visibility level is **_internal_**. In contracts, the fallback function can be **_external_** or **_public_**. In interfaces, all the functions should be declared as **_external_**. Explicitly define function visibility to prevent confusion. 

**Recommendation: **Avoid ambiguity: explicitly declare visibility levels. [Link](https://solidity.readthedocs.io/en/develop/contracts.html#visibility-and-getters)

**Slither:**

Slither is a Solidity static analysis framework which runs a suite of vulnerability detectors, prints visual information about contract details, and provides an API to easily write custom analyses. Slither enables developers to find vulnerabilities, enhance their code comprehension, and quickly prototype custom analyses. We got a report with a few warnings and errors. 




All the warnings coming there we have discussed in manual test methodology. 

**Manticore: **

[Manticore](https://github.com/trailofbits/manticore) is a symbolic execution tool for the analysis of smart contracts and binaries. It executes a program with symbolic inputs and explores all the possible states it can reach. It also detects crashes and other failure cases in binaries and smart contracts. 

Manticore results throw some warnings which are similar to Slither warning. 


Manticore gave a few warnings at a few places declaration has been of  shadows of  an existing declaration. 

**Remix IDE**

Remix was able to compile code perfectly and was behaving according to the required property. Attaching the screenshot. 




There was no error/warning at Remix IDE.

**Mythx:**

MythX is a security analysis tool and API that performs static analysis, dynamic analysis, symbolic execution, and fuzzing on Ethereum smart contracts. MythX checks for and reports on the common security vulnerabilities in open industry-standard SWC Registry.

There are many contracts within the whole file. I have separately put them for analysis.  Below are the reports generated for each contract separately. 



1. Contract Context: No vulnerability found. There were a few warnings around SWC-101.











2. Contract TokenBumo:  No vulnerability found. 








3. Contract ERC20:  No vulnerability found. 

 





4. Contract SafeMath: No vulnerability found. 
5. Contract Pausable: No vulnerability found. 




All the pdf copies of the report can be found at this [link.](https://drive.google.com/drive/folders/19lQh5dVdOyX4Ow5cT7tZ4iAzr_1XQqk0?usp=sharing)

**Disclaimer**

The audit does not give any warranties on the security of the code. One audit cannot be considered enough. We always recommend proceeding with several independent audits and

a public bug bounty program to ensure the security of the code. Besides, a security audit, please don’t consider this report as investment advice.

**Summary**

The use of smart contracts is simple and the code is relatively small. Altogether the code is written and demonstrates effective use of abstraction, separation of concern, and modularity. But there are a few issues/vulnerabilities to be tackled at various security levels, it is recommended to fix them before deploying the contract on the main network.  Given the subjective nature of some assessments, it will be up to the Bumo team to decide whether any changes should be made.

[ Full report link ]: https://docs.google.com/document/u/1/d/e/2PACX-1vSLk-gLs4caEbOBbOPlb6RW-DVjxX_9HGgWzdnjY6yRqcMhrEiOUTiKrjiGNSaFtAjgc71OHz-nWzZz/pub
