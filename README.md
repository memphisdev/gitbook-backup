---
cover: .gitbook/assets/Banner- Memphis.dev streaming .jpg
coverY: 0
---

# Step 1 - Installation

We are happy to announce Memphis #1 hackathon [#SaveZakar](https://memphis.dev/blog/save-zakar-hackathon/)!📣 📣 📣&#x20;

<figure><img src=".gitbook/assets/Hackathon banner.png" alt=""><figcaption></figcaption></figure>

For more information and sign up enter [#SaveZaker hackathon](https://memphis.dev/blog/save-zakar-hackathon/).

## What is Memphis.dev?

**Memphis.dev** is a next-generation alternative to traditional streaming engines.

It accelerates the development of real-time applications that require large volumes of streamed and enriched data, multiple protocols, and with minimum platform operations.

Highly resilient, distributed architecture, cloud-native, and run on any Kubernetes, on any cloud without zookeeper, bookeeper, or JVM.

**Memphis focuses on four pillars -**

1. Reliability - Queues and brokers are a mission-critical component in the modern application architecture and should be highly available and stable as possible.
2. Performance and Efficiency - Provide great performance while maintaining efficient resource consumption.
3. Developer Experience - Enable rapid development and ultra-short time-to-production.
4. Observability - Increase observability, integrations with 3rd-party monitoring tools, real-time notifications, stream lineage, and therefore troubleshooting time reduction.

## **Quick Start**

### **Kubernetes**

Stable -

{% code lineNumbers="true" %}
```bash
helm repo add memphis https://k8s.memphis.dev/charts/ --force-update && 
helm install memphis memphis/memphis --create-namespace --namespace memphis --wait
```
{% endcode %}

Latest -

<pre class="language-bash" data-line-numbers><code class="lang-bash">helm repo add memphis https://k8s.memphis.dev/charts/ --force-update &#x26;&#x26; 
<strong>helm install --set memphis.image="memphisos/memphis:1.1.1" memphis memphis/memphis --create-namespace --namespace memphis --wait
</strong></code></pre>

More information can be found in the [Memphis k8s deployment](deployment/kubernetes/) documentation.

### **Docker compose (Syntax for v2)**

Stable -&#x20;

{% code overflow="wrap" %}
```bash
curl -s https://memphisdev.github.io/memphis-docker/docker-compose.yml -o docker-compose.yml && docker compose -f docker-compose.yml -p memphis up
```
{% endcode %}

Latest -

{% code overflow="wrap" %}
```bash
curl -s https://memphisdev.github.io/memphis-docker/docker-compose-latest.yml -o docker-compose-latest.yml && docker compose -f docker-compose-latest.yml -p memphis up
```
{% endcode %}

More information can be found in the [Memphis Docker deployment](deployment/docker-compose.md) documentation.

## Walkthrough

{% embed url="https://app.storylane.io/share/upo0paxdvynz" %}

## High-level diagram

<figure><img src=".gitbook/assets/overview (1).jpeg" alt=""><figcaption></figcaption></figure>
