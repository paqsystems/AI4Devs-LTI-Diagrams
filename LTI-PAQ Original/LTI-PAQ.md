# ATS LTI — Descripción y Lean Canvas

	## Descripción breve
	**LTI** es un **ATS enterprise para LATAM** con foco en **colaboración en tiempo real**, **automatización orquestada** y **IA ética** (resumen de entrevistas, anonimización avanzada para mitigar sesgos). Integra nativamente **Outlook/Teams**, **LinkedIn/bolsas**, **e-sign** y **HRIS** corporativos. Seguridad **privacy-by-design** (Always Encrypted + TDE + auditoría) sobre **Azure** y **SQL Server Enterprise**.

	## Valor agregado y ventajas competitivas
	- **Anonimización avanzada** y trazabilidad ética en screening (equidad).
	- **Orquestación event-driven** (Sagas) para procesos robustos y auditables.
	- **Colaboración** integrada (Teams/chat, @mentions, feedback en contexto).
	- **Localización LATAM** (idioma, normativas, idiosincrasia) con arquitectura abierta.
	- Evita **lock-in**: APIs abiertas + conectores modulares hacia HRIS/e-sign/job boards.

	## Funciones principales
	- Requisición y publicación multiportal.
	- Recepción de CV, parsing automático, **scoring** y vista **anónima**.
	- Entrevistas (agenda integrada) + **resumen automático por IA**.
	- Oferta y **firma digital** con sincronización HRIS.
	- Auditoría completa, RBAC y gestión de consentimiento.

	---

	## Lean Canvas
	**Problema**: Procesos de selección fragmentados, sesgos en filtro inicial, baja colaboración operativa y mucha manualidad en integraciones.  
	**Segmentos de clientes**: Corporaciones **enterprise** multinacionales en **LATAM** (HR/TA).  
	**Propuesta de valor**: *ATS colaborativo, automatizado y ético para LATAM*.  
	**Solución**: Workflow orquestado + IA explicable + integraciones corporativas.  
	**Métricas clave**: Time-to-hire, cost-per-hire, % tareas automatizadas, satisfacción de hiring managers, métricas de fairness.  
	**Ventaja competitiva**: Anonimización avanzada + orquestación con Sagas + foco regional.  
	**Canales**: Partners HRIS, Microsoft ecosystem, integradores locales.  
	**Estructura de costos**: Infra Azure, licencias e-sign, operación de integraciones.  
	**Ingresos**: Licencia SaaS por tenant/usuario, add-ons de IA y conectores premium.

# Casos de uso principales (MVP)

	## CU1 — Publicación y difusión de vacante
	**Actores**: Recruiter, Hiring Manager, HRBP, Integrations Hub, Orchestrator  
	**Descripción**: Requisición, aprobación y publicación multiportal.
	**Criterios (Gherkin)**:
	```
	Given requisición completa y aprobada
	When el recruiter solicita publicar
	Then se publica en portales conectados
	And se notifica a HRBP y manager
	```
		### Mermaid 
		flowchart TD
		A[Manager solicita vacante] --> B[HRBP valida y aprueba]
		B --> C[Recruiter completa detalles]
		C --> D[Evento Vacancy.Created]
		D --> E[Integrations Hub publica en portales]
		E --> F[Confirmación y tracking]
	
	## CU2 — Screening automatizado con IA y anonimización
	**Actores**: Candidate, IA Parser, Security, Recruiter, Orchestrator  
	**Descripción**: Parsing, anonimización y scoring; shortlist ciega para evaluación.
	**Criterios**:
	```
	Given un CV recibido
	When el sistema procesa y anonimiza PII
	Then genera perfil y score
	And muestra vista anónima al recruiter
	```

		### Mermaid
		flowchart TD
		A[CV recibido] --> B[Parser IA extrae datos]
		B --> C[Security: anonimización PII]
		C --> D[Calcula score de afinidad]
		D --> E[Shortlist anónima para recruiter]

		
	## CU3 — Gestión de entrevistas y feedback colaborativo
	**Actores**: Recruiter, Entrevistadores/Manager, Candidate, Integrations Hub, IA  
	**Descripción**: Coordinación de calendarios, invitaciones, feedback y resumen IA.
	**Criterios**:
	```
	Given entrevistas definidas
	When se agenda en calendarios corporativos
	Then envía invitaciones
	And registra feedback + resumen automático
	```
	
		### Mermaid
		flowchart TD
		A[Seleccionar candidatos] --> B[Consultar disponibilidad calendarios]
		B --> C[Enviar invitaciones y recordatorios]
		C --> D[Registrar feedback]
		D --> E[Generar resumen IA]
		E --> F[Consolidar evaluación]
		
# Modelo de Datos — ERD (v1)

	Incluye entidades principales, atributos y relaciones. Diseño **privacy-by-design**, con PII cifrada (Always Encrypted), auditoría y multiempresa (Tenant).

	- Entidades clave: Tenant, User/Role, Requisition, Vacancy, Candidate (+PII), Application, Interview, Offer, IntegrationConnector, AuditLog, Consent, etc.
	- Relaciones: Tenant 1–N {User, Requisition, Vacancy, Candidate...}, Vacancy 1–N {JobPosting, Application}, Candidate 1–N {Application, Education, Experience, Documents, Skills}, Application 1–N {Interview, Offer, Events}, etc.
	- Índices prioritarios para búsquedas por Tenant/Status/Stage, y outbox para publicación de eventos.

	> Ver `ERD_DDL.sql` para definiciones base de tablas núcleo.

	## Diseño base de Datos en comandos SQL
	
	-- ATS LTI - ERD DDL (extracto base)
	-- Convenciones: PK UNIQUEIDENTIFIER (NEWSEQUENTIALID), auditoría, Always Encrypted para PII, Temporal Tables donde aplica

	CREATE TABLE dbo.Vacancy (
	  Id UNIQUEIDENTIFIER NOT NULL DEFAULT NEWSEQUENTIALID() PRIMARY KEY,
	  TenantId UNIQUEIDENTIFIER NOT NULL,
	  RequisitionId UNIQUEIDENTIFIER NOT NULL,
	  Code NVARCHAR(40) NOT NULL,
	  Title NVARCHAR(200) NOT NULL,
	  EmploymentType NVARCHAR(40) NOT NULL,
	  Seniority NVARCHAR(40) NULL,
	  DescriptionMd NVARCHAR(MAX) NULL,
	  Location NVARCHAR(150) NULL,
	  Status NVARCHAR(30) NOT NULL,
	  OpenDate DATETIME2 NOT NULL,
	  CloseDate DATETIME2 NULL,
	  OwnerId UNIQUEIDENTIFIER NOT NULL,
	  CreatedAt DATETIME2 NOT NULL DEFAULT SYSUTCDATETIME(),
	  CreatedBy UNIQUEIDENTIFIER NULL,
	  UpdatedAt DATETIME2 NULL,
	  UpdatedBy UNIQUEIDENTIFIER NULL,
	  RowVersion ROWVERSION
	);
	CREATE INDEX IX_Vacancy_Tenant_Status ON dbo.Vacancy (TenantId, Status, OpenDate DESC);

	CREATE TABLE dbo.Candidate (
	  Id UNIQUEIDENTIFIER NOT NULL DEFAULT NEWSEQUENTIALID() PRIMARY KEY,
	  TenantId UNIQUEIDENTIFIER NOT NULL,
	  AnonHash NVARCHAR(128) NOT NULL,
	  PrimaryEmail NVARCHAR(320) COLLATE Latin1_General_BIN2 ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = CEK1, ENCRYPTION_TYPE = DETERMINISTIC, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NULL,
	  Phone NVARCHAR(40) COLLATE Latin1_General_BIN2 ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = CEK1, ENCRYPTION_TYPE = DETERMINISTIC, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NULL,
	  Country NVARCHAR(80) NULL,
	  City NVARCHAR(120) NULL,
	  ConsentAt DATETIME2 NULL,
	  IsBlocked BIT NOT NULL DEFAULT 0,
	  CreatedAt DATETIME2 NOT NULL DEFAULT SYSUTCDATETIME(),
	  CreatedBy UNIQUEIDENTIFIER NULL,
	  UpdatedAt DATETIME2 NULL,
	  UpdatedBy UNIQUEIDENTIFIER NULL,
	  RowVersion ROWVERSION
	);
	CREATE UNIQUE INDEX UX_Candidate_Tenant_Hash ON dbo.Candidate (TenantId, AnonHash);

	CREATE TABLE dbo.Application (
	  Id UNIQUEIDENTIFIER NOT NULL DEFAULT NEWSEQUENTIALID() PRIMARY KEY,
	  TenantId UNIQUEIDENTIFIER NOT NULL,
	  CandidateId UNIQUEIDENTIFIER NOT NULL,
	  VacancyId UNIQUEIDENTIFIER NOT NULL,
	  AppliedAt DATETIME2 NOT NULL,
	  Source NVARCHAR(80) NULL,
	  Stage NVARCHAR(30) NOT NULL,
	  Score DECIMAL(5,2) NULL,
	  IsAnonymizedView BIT NOT NULL DEFAULT 1,
	  CreatedAt DATETIME2 NOT NULL DEFAULT SYSUTCDATETIME(),
	  CreatedBy UNIQUEIDENTIFIER NULL,
	  UpdatedAt DATETIME2 NULL,
	  UpdatedBy UNIQUEIDENTIFIER NULL,
	  RowVersion ROWVERSION
	);
	CREATE INDEX IX_Application_Tenant_Vacancy ON dbo.Application (TenantId, VacancyId, Stage);

	CREATE TABLE dbo.Interview (
	  Id UNIQUEIDENTIFIER NOT NULL DEFAULT NEWSEQUENTIALID() PRIMARY KEY,
	  TenantId UNIQUEIDENTIFIER NOT NULL,
	  ApplicationId UNIQUEIDENTIFIER NOT NULL,
	  ScheduledStart DATETIME2 NOT NULL,
	  ScheduledEnd DATETIME2 NOT NULL,
	  Location NVARCHAR(120) NULL,
	  Status NVARCHAR(20) NOT NULL,
	  CalendarEventId NVARCHAR(200) NULL,
	  CreatedAt DATETIME2 NOT NULL DEFAULT SYSUTCDATETIME(),
	  CreatedBy UNIQUEIDENTIFIER NULL,
	  UpdatedAt DATETIME2 NULL,
	  UpdatedBy UNIQUEIDENTIFIER NULL,
	  RowVersion ROWVERSION
	);
	CREATE INDEX IX_Interview_Tenant_App_Start ON dbo.Interview (TenantId, ApplicationId, ScheduledStart);

# Diseño de Sistema a Alto Nivel

	- Frontend: React+TS (Recruiter/Manager, Portal Candidato).
	- API Gateway: Azure API Management (OIDC, rate limit, versionado).
	- Servicios de dominio (.NET 8): Recruitment, Candidate, Applications, Interview, Offer, Security, Integrations.
	- Procesamiento asíncrono: Workflow Orchestrator (Azure Functions + Service Bus, Sagas).
	- Datos: Azure SQL Server (Always Encrypted, Temporal), Blob Storage, Key Vault.
	- Observabilidad: Application Insights + Azure Monitor.
	- CI/CD: Azure DevOps.

	Ver diagramas Mermaid adjuntos:
	- `ATS_LTI_Arquitectura_Capas.mmd`
	- `ATS_LTI_Flujo_E2E.mmd`

# C4 Model

	## C4 Model - Container Level (ATS LTI)
	C4Container
		title ATS LTI - Container Diagram
		System_Boundary(s1, "ATS LTI") {
			Container(webapp, "Web App (React)", "TypeScript/React", "Portales Recruiter y Candidato")
			Container(api, "API Gateway / BFF", ".NET 8", "Agrega endpoints y autentica usuarios")
			Container(domain, "Domain Services", ".NET 8 microservicios", "Lógica de negocio modular")
			Container(workflow, "Workflow Orchestrator", "Azure Functions + Service Bus", "Coordina flujos y sagas")
			ContainerDb(db, "SQL Server", "Relacional", "Datos estructurados, Always Encrypted")
			Container(blob, "Blob Storage", "Azure Storage", "CVs y documentos")
		}
		Rel(webapp, api, "Consume APIs REST")
		Rel(api, domain, "Expone servicios de negocio")
		Rel(domain, workflow, "Emite eventos de proceso")
		Rel(workflow, db, "Lectura/escritura")
		Rel(workflow, blob, "Acceso a documentos")
		Rel(api, db, "Lectura/escritura controlada")
		
	## C4 Model - Context Level (ATS LTI)
	C4Context
		title ATS LTI - Context Diagram
		Enterprise_Boundary(b0, "ATS LTI System") {
			Person(recruiter, "Recruiter/Manager", "Publica vacantes, analiza candidatos y genera ofertas.")
			Person(candidate, "Candidate", "Aplica y sigue sus postulaciones.")
			System(atslti, "ATS LTI", "Sistema de reclutamiento con IA y anonimización")
			System_Ext(hris, "HRIS", "Sistema core de RRHH")
			System_Ext(linkedin, "LinkedIn/Job Boards", "Publicación y recepción de postulaciones")
			System_Ext(mail, "Microsoft Graph", "Correo y calendario corporativo")
			System_Ext(esign, "E-Sign", "Firma electrónica de ofertas (DocuSign/Adobe Sign)")
		}
		Rel(recruiter, atslti, "Crea vacantes / gestiona procesos")
		Rel(candidate, atslti, "Postula y consulta estado")
		Rel(atslti, hris, "Integra datos laborales y nómina")
		Rel(atslti, linkedin, "Publica y recibe postulaciones")
		Rel(atslti, mail, "Calendarios, correos y recordatorios")
		Rel(atslti, esign, "Genera y gestiona documentos firmados")

	## C4 Model - Component Level (Workflow Orchestrator)
	C4Component
		title Workflow Orchestrator - Component Diagram
		Container_Boundary(cb1, "Workflow Orchestrator") {
			Component(sub, "Subscriber Functions", "Azure Functions", "Suscriben a eventos del bus y ejecutan acciones.")
			Component(saga, "Saga Manager", "C#", "Coordina pasos y compensaciones.")
			Component(rule, "Rule Engine", "C#", "Evalúa condiciones dinámicas para rutas de flujo.")
			Component(pub, "Publisher", "C#", "Publica eventos resultantes a otros servicios.")
			Component(store, "Outbox Handler", "C#", "Lee/actualiza OutboxEvent para garantizar entrega at-least-once.")
		}
		Rel(sub, saga, "Invoca")
		Rel(saga, rule, "Evalúa reglas de negocio")
		Rel(saga, pub, "Dispara eventos externos")
		Rel(pub, store, "Confirma publicación")

	# ATS LTI — C4 Deep Dive (Workflow Orchestrator)

	## Niveles del modelo C4 incluidos
	1. **Context** — vista general del sistema y actores externos.  
	2. **Container** — distribución de responsabilidades técnicas.  
	3. **Component** — detalle interno del Workflow Orchestrator.

	## Descripción
	El **Workflow Orchestrator** gestiona la coordinación de procesos entre microservicios usando Azure Service Bus y Azure Functions.  
	Implementa patrones *Saga*, *Outbox* y *Event Choreography*, asegurando consistencia eventual sin acoplar los servicios de dominio.

	### Responsabilidades principales
	- Orquestar eventos complejos (creación de vacantes, recepción de postulaciones, entrevistas, ofertas).  
	- Manejar reintentos y compensaciones ante errores.  
	- Publicar nuevos eventos para mantener sincronizados a los servicios de dominio y sistemas externos (LinkedIn, HRIS, etc.).  
	- Proveer trazabilidad completa de cada flujo mediante logs y correlaciones.

	### Tecnología
	- **Azure Functions** (event-driven con triggers de Service Bus).  
	- **C# .NET 8** para lógica de saga y orquestación.  
	- **Azure Service Bus Topics/Queues** para mensajería confiable.  
	- **SQL Server** (OutboxEvent) + **Application Insights** (trazabilidad).

	---
	**Nota:** Los diagramas `.mmd` pueden renderizarse con [Mermaid CLI](https://github.com/mermaid-js/mermaid-cli):
	```bash
	mmdc -i C4_Context.mmd -o C4_Context.svg
	mmdc -i C4_Container.mmd -o C4_Container.svg
	mmdc -i C4_Component.mmd -o C4_Component.svg
	```
	
# Extras

	## En archivo slides_backlog_zip.zip se anexan documentación adicional a la documentación solicitada, entre ellas un checklist de los puntos a realizar
	