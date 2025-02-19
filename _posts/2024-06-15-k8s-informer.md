---
layout: post
title: kubernetes informer 机制详解
subtitle: 通过 Kubernetes 源码梳理 informer 机制的实现原理和工作流程
excerpt_image: https://github.com/huziuncle/huziuncle.github.io/blob/master/assets/images/k8s/k8s.webp?raw=true
categories: Kubernetes
tags: [Kubernetes]
---

![banner](https://github.com/huziuncle/huziuncle.github.io/blob/master/assets/images/k8s/k8s.webp?raw=true)

## 简介
Kubernetes Informer 机制是 Kubernetes 客户端库（Client-Go）中的一个重要组件，用于监控和缓存 Kubernetes API 对象的变化。

Informer 提供了一种高效、可靠的方式来跟踪 Kubernetes 集群中的资源状态，并在资源发生变化时触发相应的处理逻辑。

## 实现机制
![informer](https://www.plantuml.com/plantuml/png/XLNVJzjs47_dh_1Ufe0qAbxtC1MrGqLZWz5rVPZsuCPVH4PYUuv35qsJ0eGG88HCIo4G5X8QscX022gDyYE8FsQ-h-sd_Wix--pCLTagvU5ykH-Vk_lSVU_7TL7JOn5PYedgo2Vw8einGLUI8oDZmhIye6Wnfg4y9iopXIWBwug67sE2qS-BQYJEj6McyUrDsDs3RfldLip-XLd-7GhdPha3ws-ijsdUAvXNVQXicuTfEAb0jigBNQEtRVJRRYIuAC5g0x8jVdGEHorpy0Tg7hAeBavDJyqQdGlNwhceXnSVYR8KPHhgxDIXTRi1kplGIfkrfjMic-Kchsu4yusui8UH2FRDhLbisALZoEx3LjKkha0zRHOoVwTM8D-tDtPG840eg8hAsrdhkkl8cX9cyJXP4-vcnSotK4JvvmHBiCcm7b7a6IMyn2HIIqnAg2w0E2aYEekXwChOxi-59POKFlj289iVb6S__HW8w1b68t7TjNJhcPObzWlJ0jK4UuKzTZ-_IwhCkYrRjHnaoz3lOGMyVCttMYZW1Eo3EbnseLT4U_Fw10OlFYY9rB1nmr-qGcxIgOGS7dqtEBIQDREvJ_-Zlsk2SLVjVWddW0PVCrtyHeonk2g6sT3rlR2fGIZY5Y9zBqOJp0dBZpQDNXaPmFUwi7ickFz_nPBl1ZchrZ3hFRDuzH-W18846pt-vocLv2M1Lwk279yOfUHOitLLiulD47OQGpjfIIUWGa1F0OgNHYVbTDltlQSgqWHQvenEdXyNZCvVH39oH6FyKJFT-JnGP5rJeb5lH7OvmxC5evVvTtzm1CEHeUmD51FmqgLrM-7vKwEppJiTufwLMaTSl7RAJrRSYFpjYjdOzWegNC9MOmO9CYbBJvqLmJ2qVnx8tHBadDKSRjamTJ22n-uPHL5niOScmfoY4ei82xd45qMDnkhV1oJUehkQn6EpVystwbvTcDjghI5DNJTfZZ8fT5CUkpe_39MtcK8cOCaExPdCj3ZjEhc_btPyKfA-N6Qoxjq5N6XOFyKQLJ_9Z1V3lo9mr7WK08fgf-tLBjzf8W7yvQ7pSIVgSGaZQf5d2PsP_GF2IlqUmgKJSKTvKw8p_MHWSt6sni31JCUByeO-3IiQMvuOdrEaNtyRTJQtahPJLO_r4biG4r7T3-eGav3UcMny-ArejDsKobSxr7hJW4pEuKsw3ONy_mLxx3mLH8ZvXEoM-5JHbhxrZvL0e_gW_L2kO5Wgm5caNXsR1vbrUwqnp-9AGWkpu1mfWtbqp2zU1X-Dq8Cd3qUflHW5iXd8dX7SMHcfS6zMVg9AeivGm3aodL5fb9U21kwtqUdXMiBjcPqw9hMLmXtViKjzg5K-MaCdPMNFQOpFiGVkmVPo35RvnIckdVNwfPLw1OLi8AkrTWDRPLea3R7EH68HtJue80Z_pOYAIUYt-NuRYO4lLGZldZ4emNhMxBscbulQ3ArTkzQ6QeO6EnfuEGIoywbudycIzyB_0m00)
## 工作流程
![informer-seq](https://www.plantuml.com/plantuml/png/RL7HRXCn47ptLvWdu279-mcXLYqLaH2jYge-k_OUPyctNjPx9FmzMPopujAtP6PsF3DtLSInB7CoxoSHAiDkHycX8bkVvrs1NmkWXqs8xYUxsRoJ2MQmalyOunR9kCmlmCQGOua-aaEnIm7kW38vXh370CSzZfbdOEkABJ6-8NlWxA6KN2NKAMZbliKYWDM6KgU9-anbOZXsL80axcv_Tw_a6CVSiJwZS4wftdhXtlSHnWHUQYuUZJ7Qqsu-Qu_1VeV3Z_zhzfhh4Bxy1fIl3aCwaIUmSGY7S_ErMwDfi2w41dTdZmk-jcb5UekstPRrXXIdDDXKVIjCQOMV2l0fyTFjMrBAP3-u46ucfH2ESZDzRC5qkk4YnLP36aKRgtErGP-ZJFSaCMDfAXu7MmzvNF_h0jSOdftuwGphHnpiGP4wQity-ZM_fVEsELFZwl732T5VRS9gT0j9N5ahnN2inXU3XbY4uyiYS37Hlr4-RJ1B7ADtMifS0OPbJdy1)

## 作用
+ 减少 API Server 压力：通过本地缓存避免频繁访问 API Server，从而减少 API Server 的压力。
+ 实时性：通过 Watch 机制，Informer 可以及时获取资源对象的变化，确保数据的实时性。
+ 简化开发：Informer 提供了便捷的接口，开发者可以方便地监听资源对象的变化并进行处理，简化了 Kubernetes 应用的开发。
