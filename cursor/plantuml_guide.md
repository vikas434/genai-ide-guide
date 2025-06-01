# PlantUML Setup and C4 Diagram Creation Guide

## 1. Prerequisites Installation

### 1.1 Install Java (OpenJDK)
```bash
# Using Homebrew on macOS
brew install openjdk

# Create a symbolic link to make Java available system-wide
sudo ln -sfn $(brew --prefix)/opt/openjdk/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk.jdk
```

### 1.2 Install PlantUML
```bash
# Using Homebrew on macOS
brew install plantuml

# Verify installation
plantuml -version
```

### 1.3 Install GraphViz (required for some diagrams)
```bash
brew install graphviz
```

## 2. Creating C4 Model Diagrams

### 2.1 C1 - System Context Diagram
```plantuml
@startuml C1_Context
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

title System Context diagram for Civic Information System

Person(citizen, "Citizen", "A member of the public seeking civic information")
System(civic_info, "Civic Information System", "Provides access to government information and services")
System_Ext(external_apis, "External APIs", "Third-party data sources")

Rel(citizen, civic_info, "Uses")
Rel(civic_info, external_apis, "Gets data from")

@enduml
```

### 2.2 C2 - Container Diagram
```plantuml
@startuml C2_Container
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

title Container diagram for Civic Information System

Person(citizen, "Citizen", "A member of the public seeking civic information")
System_Boundary(c1, "Civic Information System") {
    Container(web_app, "Web Application", "React", "Provides civic information via web interface")
    Container(api, "API Gateway", "FastAPI", "Handles all API requests")
    ContainerDb(db, "Database", "PostgreSQL", "Stores civic data")
}

System_Ext(external_apis, "External APIs", "Third-party data sources")

Rel(citizen, web_app, "Uses", "HTTPS")
Rel(web_app, api, "Makes API calls to", "JSON/HTTPS")
Rel_Back(api, db, "Reads from and writes to", "SQL")
Rel(api, external_apis, "Gets data from", "HTTPS")

@enduml
```

### 2.3 C3 - Component Diagram
```plantuml
@startuml C3_Component
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title Component diagram for API Gateway

Container_Boundary(api, "API Gateway") {
    Component(auth, "Auth Controller", "FastAPI", "Handles authentication")
    Component(representative, "Representative Controller", "FastAPI", "Manages representative data")
    Component(news, "News Controller", "FastAPI", "Manages news content")
    Component(events, "Events Controller", "FastAPI", "Manages civic events")
}

Rel(auth, representative, "Uses")
Rel(auth, news, "Uses")
Rel(auth, events, "Uses")

@enduml
```

### 2.4 C4 - Code Diagram
```plantuml
@startuml C4_Code
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Code.puml

title Code diagram for Representative Controller

Class(RepresentativeController) {
    +get_representative(slug: str)
    +list_representatives()
    +update_representative(data: dict)
}

Class(RepresentativeService) {
    +find_by_slug(slug: str)
    +list_all()
    +update(data: dict)
}

Class(Representative) {
    +id: int
    +name: str
    +position: str
    +slug: str
}

RepresentativeController --> RepresentativeService : uses
RepresentativeService --> Representative : manages

@enduml
```

### 2.5 Sequence Diagram
```plantuml
@startuml sequence
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Sequence.puml

title Sequence diagram for viewing representative information

actor Citizen
participant "Web App" as web
participant "API Gateway" as api
participant "Auth Service" as auth
participant "Representative Service" as rep
database "Database" as db

Citizen -> web : Views representative page
web -> api : GET /representatives/{slug}
api -> auth : Validate request
auth --> api : Request validated
api -> rep : Get representative details
rep -> db : Query representative data
db --> rep : Return data
rep --> api : Return representative details
api --> web : Return JSON response
web --> Citizen : Display representative info

@enduml
```

## 3. Testing and Generating Diagrams

### 3.1 Basic Testing
```bash
# Test PlantUML installation
plantuml -testdot

# Generate a single diagram
plantuml filename.puml

# Generate all diagrams in current directory
plantuml *.puml
```

### 3.2 Output Formats
```bash
# Generate PNG (default)
plantuml diagram.puml

# Generate SVG
plantuml -tsvg diagram.puml

# Generate PDF
plantuml -tpdf diagram.puml
```

### 3.3 Batch Processing
```bash
# Generate all diagrams recursively
plantuml -recursive .

# Generate with specific config
plantuml -config config.cfg diagram.puml
```

## 4. Best Practices

1. **File Organization**
   - Keep diagram files in a dedicated `diagrams` directory
   - Use meaningful file names (e.g., `c1_context.puml`, `c2_container.puml`)
   - Maintain separate files for different diagram types

2. **Naming Conventions**
   - Use descriptive names for components
   - Follow consistent naming patterns
   - Add clear descriptions for each element

3. **Documentation**
   - Add comments to explain complex relationships
   - Include a brief description at the start of each diagram
   - Document any custom styles or configurations

4. **Version Control**
   - Commit both `.puml` source files and generated diagrams
   - Include PlantUML version in documentation
   - Document any special requirements

## 5. Troubleshooting

### Common Issues and Solutions

1. **Java Not Found**
   ```bash
   # Check Java installation
   java -version
   
   # Set JAVA_HOME if needed
   export JAVA_HOME=$(/usr/libexec/java_home)
   ```

2. **GraphViz Issues**
   ```bash
   # Verify GraphViz installation
   dot -V
   
   # Reinstall if needed
   brew reinstall graphviz
   ```

3. **PlantUML Errors**
   ```bash
   # Run with debug output
   plantuml -verbose diagram.puml
   
   # Check syntax
   plantuml -syntax diagram.puml
   ```

## 6. Additional Resources

- [PlantUML Official Documentation](https://plantuml.com/)
- [C4 Model Official Site](https://c4model.com/)
- [PlantUML C4 Macros](https://github.com/plantuml-stdlib/C4-PlantUML)