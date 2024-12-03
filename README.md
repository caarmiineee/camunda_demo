# Camunda BPM Project with Spring Boot

## Project Overview
This project integrates **Camunda BPM** with **Spring Boot** to manage business workflows effectively. The workflows include decisions based on user inputs and process outcomes, such as booking a seat on a ship, determining ticket types, and handling errors using BPMN (Business Process Model and Notation).

### Key Features
1. **BPMN Workflow Design:**
   - Uses Camunda Modeler to define workflows visually.
   - Supports gateways, events, user tasks, and service tasks.
2. **Form Management:**
   - For **user tasks** (indicated by a person icon in the BPMN model), the workflow displays forms to capture user inputs.
   - Form fields can include validation (e.g., regex patterns for input).
3. **Business Logic Execution:**
   - For **business tasks** (e.g., service tasks), logic is executed in Java delegate classes.
4. **Spring Boot Integration:**
   - Simplifies application setup and deployment.
   - Allows the usage of Spring Beans for delegates and other logic.
5. **Error Handling:**
   - Uses BPMN error events to manage process exceptions (e.g., "Fall Overboard").

---

## How to Run

### Prerequisites
- **Java 17+** installed.
- **Maven 3.6+** installed.
- **Camunda Modeler** (optional, for workflow editing).

### Steps
1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd camunda-springboot-project
Build the project:

bash
Copy code
mvn clean install
Run the application:

bash
Copy code
mvn spring-boot:run
Access the Camunda web interface:

URL: http://localhost:8080
Default credentials:
Username: demo
Password: demo
Running a Process
Deploy your BPMN model (e.g., process.bpmn) through the Camunda interface or by placing it in the resources folder.
Start the process manually using the Camunda Tasklist or via the REST API:
URL: http://localhost:8080/engine-rest/process-definition/key/{processKey}/start
Project Structure
src/main/java/
com.example.camunda:
Java delegate classes (e.g., ReserveSeatOnBoat.java) for executing business logic.
src/main/resources/
process.bpmn: The main BPMN file defining the workflow.
application.properties: Spring Boot configuration.
Key Components
BPMN Workflow
User Tasks:

Indicated by a person icon in the BPMN model.
Require user input via forms. Forms can include:
Text fields.
Dropdowns.
Validation rules (e.g., regex).
Example configuration in the Camunda Modeler:
xml
Copy code
<formField id="money" label="Available Money" type="string" />
Service Tasks:

Execute logic using Java delegates.
Example: Determining ticket type based on available funds.
Gateways & Events:

Gateways: Define decision points (e.g., "Where are you going?").
Events:
Error events for exceptions like insufficient funds.
End events to mark process completion.
Delegate Example: ReserveSeatOnBoat.java
java
Copy code
@Named
public class ReserveSeatOnBoat implements JavaDelegate {
    @Override
    public void execute(DelegateExecution delegateExecution) throws Exception {
        String money = (String) delegateExecution.getVariable("money");
        double moneyDouble = Double.parseDouble(money);

        String ticketType;
        if (moneyDouble >= 10000) {
            ticketType = "First Class";
        } else if (moneyDouble >= 5000) {
            ticketType = "Business Class";
        } else if (moneyDouble <= 10) {
            ticketType = "Stowaway Class";
            throw new BpmnError("Fall_Overboard", "A Cheap ticket has led to a small wave throwing you overboard");
        }
        delegateExecution.setVariable("ticketType", ticketType);
    }
}
Spring Boot Configuration
Dependencies:

camunda-spring-boot-starter
spring-boot-starter-web
Other required libraries.
application.properties:

properties
Copy code
server.port=8080
camunda.bpm.admin-user.id=demo
camunda.bpm.admin-user.password=demo
camunda.bpm.filter.create=All Tasks
Customization
Adding New Processes:
Create a new BPMN file in the resources folder.
Implement Java Delegates for service tasks.
Validation Rules:
Example: Use form fields in the Camunda Modeler to validate user input (e.g., custom regex for currency).
Troubleshooting
Issue: Process not deploying.
Solution: Ensure the BPMN file is correctly formatted and placed in resources.
Issue: Error during execution.
Solution: Check the Java delegate logic and BPMN error handling configurations.
Additional Resources
Camunda Documentation
Spring Boot Documentation
Camunda Modeler
go
Copy code
