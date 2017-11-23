---
title: Elasticsearch
permalink: /docs/elasticsearch/
---

### 1. 개요

아파치 루신(Apache Lucene)을 기반으로 개발된 오픈소스 분산 검색 엔진이다.

> 루신(Lucene)은 자바로 개발된 오픈소스 정보 검색 라이브러리이다.

### 2. 특징

- 분산 (distributed) + 확장성

  Elasticsearch는 규모가 수평적으로 늘어나도록 설계되어 있기 때문에 더 많은 용량이 필요하면 그저 노드를 추가하고 클러스터가 인식할 수 있게 하면 된다.

- 고가용성 (High availability)

  Elasticsearch는 동작중에 죽은 노드를 감지하고 삭제하며 사용자의 데이터가 안전하고 접근 가능하도록 유지한다.

- 멀티 태넌시 (Multi-tenancy)

  클러스터는 여러개의 인덱스들을 저장하고 관리할 수 있으며, 독립된 하나의 쿼리 혹은 그룹 쿼리로 여러 인덱스의 데이터 검색 가능

- 전문 검색 (Full text search)

  강력한 전문검색을 지원한다.

- 문서 중심 (Document oriented)

  구조화된 JSON문서 형식으로 저장하고 모든 필드는 기본적으로 인덱싱되며, 단일 쿼리로 빠르게 사용할 수 있다.

- Schema free

  스키마 개념이 없다.

- Restful API

  HTTP를 통한 json형식의 산단한 Restful API를 제공한다.

### 공식 레퍼런스

https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html