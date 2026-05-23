# Demo: Lectura de S3 con CodeBuild Runner

Repositorio de demostración que usa **AWS CodeBuild** como runner de GitHub Actions para leer un archivo desde **Amazon S3** e imprimir su contenido en los logs del workflow.

---

## ¿Qué hace el workflow?

1. GitHub detecta el evento `push` o `pull_request` y encola el job.
2. El evento `WORKFLOW_JOB_QUEUED` dispara el proyecto CodeBuild `gha-runner-co-2026-2`.
3. CodeBuild asume su IAM Role y descarga `log-demo.txt` desde el bucket `awscommunityday2026-jothan`.
4. El contenido del archivo se imprime en los logs de la ejecución.

---

## Estructura

```
.github/
└── workflows/
    └── leer-s3.yml    # Descarga log-demo.txt desde S3 e imprime su contenido
```

---

## Infraestructura requerida

El runner fue aprovisionado con el stack CDK del repositorio [aws-codebuild-runners-cdk](https://github.com/jothanpe/aws-codebuild-runners-cdk) con la siguiente configuración:

```typescript
{
  repoKey: 'co-2026-2',
  repoUrl: 'https://github.com/jothanpe/co-2026-2',
  additionalStatements: [
    new PolicyStatement({
      effect: Effect.ALLOW,
      actions: ['s3:GetObject'],
      resources: ['arn:aws:s3:::awscommunityday2026-jothan/log-demo.txt'],
    }),
  ],
}
```

Eso crea en AWS:
- Un **proyecto CodeBuild** llamado `gha-runner-co-2026-2` con trigger `WORKFLOW_JOB_QUEUED`.
- Un **IAM Role** con permisos base de runner más lectura del archivo `log-demo.txt`.

---

## Recursos

- [Stack CDK del runner](https://github.com/jothanpe/aws-codebuild-runners-cdk)
- [Documentación: GitHub Actions en AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/action-runner.html)
