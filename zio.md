---
title: Scala
layout: 2017/sheet
updated: 2023-09-28
category: Java & JVM
prism_languages: [scala]
intro: |
    [Zio](https://zio.dev/) is an effects library for Scala.
---

Composing Layers
---------
{: .-two-column}

### Building a ZLayer from a ZIO

```scala
case class Actioner(config: Config) {
    def doAction(): ZIO[Any, Throwable, Unit] = ???
}

val actionLayer(config: Config): ZLayer[Any, Throwable, Actioner] =
    ZLayer.succeed(new Actioner(config))

val mixinLayer: ZLayer[Any, Nothing, Actioner] =
    ZLayer {
        for {
            config <- ZIO.service[Config]
        } yield actionLayer(config)
    }.flatten
```

### Projecting a ZLayer

```scala
case class Config(
    actionConfig: ActionConfig
)

val actionLayer: ZLayer[ActionConfig, Throwable, Actioner] =
	ZLayer {
    for {
      config <- ZIO.service[ActionConfig]
    } yield new Actioner(config)
  }

val mixinLayer: ZLayer[Config, Throwable, Actioner] =
    ZLayer.service[Config].project(_.actionConfig) >>> actionLayer
```
