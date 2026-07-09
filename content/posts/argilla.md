---
title: "Argilla"
date: 2026-07-09
description: ""
tags: [""]
---

is an open-source data annotation platform. It can be used for use cases like collecting annotated data for training our own Encoder model or tweaking LLM judge prompt.
* Article relevance classification
* Query-passage relevance classification

## Setup

Self-host with [Docker Compose](https://docs.argilla.io/v2.0/getting_started/how-to-deploy-argilla-with-docker/)

```bash
curl https://raw.githubusercontent.com/argilla-io/argilla/main/examples/deployments/docker/docker-compose.yaml -o docker-compose.yaml
sed -i 's/-XX:UseSVE=0//g' docker-compose.yaml    # Need to remove the corrupted `-XX:UseSVE=0` flag
sudo docker compose up -d
```

Inside the docker-compose.yml contains port that for access (e.g. `http://localhost:{port}`) as well as default username, password, and API key which can be used for initial login. These can be viewed again in setting page.

### Setup on GCP Compute Engine

**Setup machine with network tag**

Compute Engine > VM instances > Create an instance
* Machine configuration: e2-medium
* Networking > Network tags: <machine tag>

**Setup firewall rule**

Network Security > Firewall policies > Create a firewall rule (Note: **not** policy)
* Direction: Ingress
* Target tags: <machine tag>
* Source IPv4 ranges: 0.0.0.0/0 for all IP addresses; can also specify more restrictive one
* Protocols and ports: Specified protocols and ports > TCP > 6900

Install [Docker Engine](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository) which includes also Docker Compose.
```bash
# Install Docker Engine (incl. Docker Compose)
## Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

## Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Access by http://EXTERNAL_IP:PORT

### Common issues with Setup

**Service doesn't start**

Check service status

```bash
sudo docker compose ps -a
```

If some service doesn't start (e.g. Elasticsearch), take service name and check their logs

```bash
sudo docker logs argilla-elasticsearch-1 --tail=100    # TODO may need change service name
```

If the logs show

```bash
Unrecognized VM option 'UseSVE=0'
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
```

Remove  `-XX:UseSVE=0` flag from docker-compose.yml.

## Concepts

* **Workspaces**
* **Users**: Initial user is owner. Admins and annotators can be created. User must be added to workspace (not just to Argilla server) to be able to see the datasets.
* **Datasets**: Datasets are basically data schema, annotation schema, and annotation guideline.
* **Records**: Actual data (+ annotation) of the dataset.

### Users

```bash
import argilla as rg

HOST = "http://localhost"    # or External IP of VM
PORT = 6900
API_KEY = "argilla.apikey"

client = rg.Argilla(
    api_url=f"{HOST}:{PORT}",
    api_key=API_KEY
)

## Create User ##
username = "test_user"
password = "test_password"

user_to_create = rg.User(
    username=username,
    password=password,
    # client=client,    # automatically recognized if client is instantiated before
)

created_user = user_to_create.create()
# created_user = client.users(username)


## Add User to Workspace ##
workspace = "default"

client.users(username).add_to_workspace(client.workspaces(workspace))

## List Users ##
print(list(client.users))
print(list(client.workspaces(workspace).users))
```

### Datasets

```bash
import argilla as rg

HOST = "http://localhost"    # or External IP of VM
PORT = 6900
API_KEY = "argilla.apikey"

client = rg.Argilla(
    api_url=f"{HOST}:{PORT}",
    api_key=API_KEY
)

## Create Dataset + Annotation Schema in Workspace ##
ds = "test_dataset"
ws = "default"

settings = rg.Settings(
    guidelines="""Some annotation guideline""",
    fields=[
        rg.TextField(name="q_text"),
        rg.ImageField(name="q_image"),
        rg.ChatField(name="q_chat")
    ],
    questions=[
        rg.LabelQuestion(
            name="a_label",
            labels=["label_1", "label_2", "label_3"],
        ),
        rg.MultiLabelQuestion(
            name="a_multilabel",
            labels=["multilabel_1", "multilabel_2", "multilabel_3"],
        ),
        rg.TextQuestion(
            name="a_text",
        ),
        rg.RankingQuestion(
            name="a_ranking",
            values=["candidate_1", "candidate_2", "candidate_3"],
        ),
        rg.RatingQuestion(
            name="a_rating",
            values=[1,2,3,4,5]
        ),
    ],
    distribution=rg.TaskDistribution(min_submitted=3),    # Distribute dataset to annotators
)

dataset = rg.Dataset(
    name=ds,
    workspace=ws,
    settings=settings,
)

created_dataset = dataset.create()
```

### Records in Dataset

```bash
import argilla as rg

HOST = "http://localhost"    # or External IP of VM
PORT = 6900
API_KEY = "argilla.apikey"

client = rg.Argilla(
    api_url=f"{HOST}:{PORT}",
    api_key=API_KEY
)

ds = "test_dataset"

# (...)
# created_dataset = dataset.create()
created_dataset = client.datasets(name=ds)

## Upload Records to Dataset ##
# Note: keys in fields must match what is specified in dataset creation
records = [
    rg.Record(
        id="record_1",    # Optional
        fields={
            "q_text": "Hello World, how are you?",
            "q_image": "https://upload.wikimedia.org/wikipedia/commons/thumb/1/15/Cat_August_2010-4.jpg/1280px-Cat_August_2010-4.jpg",
            "q_chat":  [
                {"role": "user", "content": "What is Argilla?"},
                {"role": "assistant", "content": "Argilla is a collaboration tool for AI engineers and domain experts to build high-quality datasets"},
            ],
        },
    ),
]

created_dataset.records.log(records)
```
