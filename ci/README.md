# th2-infra end-to-end test
This project leverages Kind (Kubernetes in Docker), Ansible and Go lang for end-to-end tests execution.

Coverage:
* deployment th2-infra components
* deployment schema components from git via ssh
* http endpoints from service namespace
* http endpoints from schema namespace

Output:
* workflow logs
* artifact logs attached to the job

## Structure
```
├── .github
|   └── e2e-test.yml                         - e2e testing Github Action
└── ci
    ├── e2e-via-ssh-deployment-playbook.yaml - ansible playbook to deploy th2 with infra-mgr gitops via ssh
    ├── th2_infra_test.go                    - tests written in Go with Terratest module
    └── kind-cluster.yaml                    - kind cluster config
```

## Settings and variables
Repository with schema contains version branch from which th2 namespace will be deployed, e.g. e2e-v101 https://github.com/th2-net/th2-infra-schema-demo/tree/e2e-v101.

Namespace with schema must be set as SCHEMA_NAMESPACE variable in the workflow.

```yaml
env:
  SCHEMA_NAMESPACE: schema-e2e-v101
```
**Note**: _CRs with any API changes must be allocated in a new versioned branch!_

Repository with schema is set as a value in e2e-via-ssh-deployment-playbook.yaml > "Deploy th2-infra-base" step
```
    infraMgr:
      git:
        repository: git@github.com:th2-net/th2-infra-schema-demo.git
```

Chart version for infra-operator must be set accordingly in th2-service chart default values:
```yaml
infraOperator:
  ...
  chart:
    git: https://github.com/th2-net/th2-infra.git
    ref: v1.1.1
    path: custom-component
```

Private key for infra-mgr is set as a repository secret and passed as E2E_PRIVATE_KEY env variable to playbook. Public key is set as e2e-test deploy key in the schema repository.