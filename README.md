# PostgreSQL
PostgreSQL ist ein leistungsfähiges, objektrelationales Open-Source-Datenbanksystem, das seit mehr als 35 Jahren aktiv entwickelt wird und sich einen guten Ruf in Bezug auf Zuverlässigkeit, Robustheit der Funktionen und Leistung erworben hat. Sie wird im MLA als Datenbank für den ScorpioBroker eingesetzt.

## Voraussetzungen
* Kubernetes 1.23+
* Helm 3.8.0+
* PV provisioner support in the underlying infrastructure

## Installation

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
   
helm install postgresql bitnami/postgresql -n scorpio --create-namespace -f values.yaml
  
# Postgis aktivieren
export POSTGRES_PASSWORD=$(kubectl get secret --namespace scorpio postgresql -o jsonpath="{.data.postgres-password}" | base64 -d)
kubectl run scorpio-postgresql-client --rm --tty -i --restart='Never' --namespace scorpio --image docker.io/bitnami/postgresql:17.2.0-debian-12-r9 --env="PGPASSWORD=$POSTGRES_PASSWORD" \
      --command -- psql --host scorpio-postgresql-primary -U postgres -d ngb -p 5432
  
ngb=# CREATE EXTENSION postgis;
ngb=# SELECT PostGIS_Version();
            postgis_version
---------------------------------------
 3.4 USE_GEOS=1 USE_PROJ=1 USE_STATS=1
(1 row)
  
```

## Parameter

| Name                    | Description                                     | Value  |
|:--------------------------------|:----------------------------------------|:-------|
| global.postgresql.auth.postgresPassword |                                 | ""     |
| global.postgresql.auth.username |                                         | ""     |
| global.postgresql.auth.password |                                         | ""     |
| global.postgresql.auth.database |                                         | ngb    |
| auth.enablePostgresUser         |                                         | true   |
| primary.resourcesPreset         | Set container resources                 | xlarge |
| primary.persistence.enabled     | Enable PostgreSQL Primary data persistence using PVC | true   |
| primary.persistence.size        | PVC Storage Request for PostgreSQL volume | 8Gi    |
| rbac.create                     | Create Role and RoleBinding (required for PSP to work) | true   |

## Links
https://github.com/IoTCrawler/ScorpioBroker

## License

Copyright © 2024 ADDIX GmbH.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.