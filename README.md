# Pay-me Android SDK

SDK Android de Pay-me para integrar funcionalidades de pago dentro de aplicaciones Android.

Este repositorio contiene las librerías necesarias para realizar la integración de Pay-me SDK Android.

---

## Requisitos técnicos

- Android 5.0 / API Level 21 o superior.
- Android Studio Jellyfish o superior.
- Gradle 8.6 o superior.
- Proyecto Android con soporte Kotlin.
- Conectividad a Internet durante el flujo.
- Backend del comercio para la generación segura del Access Token.

Las credenciales nunca deben almacenarse dentro de la aplicación móvil. El backend del comercio debe encargarse de generarlas y protegerlas.

---

## Librerías incluidas

| Archivo | Descripción |
|---|---|
| `Payme.aar` | Librería principal del SDK |
| `SecureKey3DS.aar` | Librería para autenticación 3DS |
| `VisaSensoryBranding.aar` | Librería de experiencia Visa |
| `MastercardSonic.aar` | Librería de experiencia Mastercard |

---

## Instalación

Crea la carpeta `app/libs/` en el proyecto y coloca dentro los artefactos entregados:

```text
app/
└── libs/
    ├── Payme.aar
    ├── SecureKey3DS.aar
    ├── VisaSensoryBranding.aar
    └── MastercardSonic.aar
```

---

## Configuración Gradle

Para un proyecto que utiliza Kotlin DSL (`build.gradle.kts`):

```kotlin
dependencies {
    implementation(files("libs/SecureKey3DS.aar"))
    implementation(files("libs/Payme.aar"))
    implementation(files("libs/VisaSensoryBranding.aar"))
    implementation(files("libs/MastercardSonic.aar"))
}
```

Para un proyecto que utiliza Groovy (`build.gradle`):

```groovy
dependencies {
    implementation files('libs/SecureKey3DS.aar')
    implementation files('libs/Payme.aar')
    implementation files('libs/VisaSensoryBranding.aar')
    implementation files('libs/MastercardSonic.aar')
}
```

---

## Plugins y configuración Android

Confirma que el módulo de la aplicación tenga Kotlin, `kotlin-parcelize` y `viewBinding` habilitados:

```kotlin
plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
    id("kotlin-parcelize")
}

android {
    buildFeatures {
        viewBinding = true
    }
}
```

---

## Permisos

Declara los siguientes permisos en `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
```

---

## Dependencias requeridas

Agrega las dependencias documentadas oficialmente al módulo Android:

```kotlin
dependencies {
    implementation("androidx.core:core-ktx:1.6.0")
    implementation("androidx.appcompat:appcompat:1.7.0")
    implementation("org.jetbrains.kotlin:kotlin-stdlib:1.9.0")
    implementation("androidx.recyclerview:recyclerview:1.2.1")
    implementation("androidx.constraintlayout:constraintlayout:2.1.4")
    implementation("androidx.localbroadcastmanager:localbroadcastmanager:1.1.0")

    implementation("com.squareup.retrofit2:retrofit:2.3.0")
    implementation("com.squareup.retrofit2:converter-gson:2.3.0")
    implementation("com.squareup.retrofit2:converter-scalars:2.5.0")
    implementation("com.squareup.okhttp3:logging-interceptor:3.10.0")
    implementation("com.squareup.okhttp3:okhttp:3.10.0")
    implementation("com.google.code.gson:gson:2.8.8")

    implementation("com.madgag.spongycastle:core:1.50.0.0")
    implementation("com.madgag.spongycastle:pg:1.50.0.0")
    implementation("org.bouncycastle:bcprov-jdk15on:1.56")
    implementation("com.nimbusds:nimbus-jose-jwt:7.0.1")

    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:0.30.1")
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:0.30.1")
}
```

> Si el proyecto utiliza versiones superiores, valida su compatibilidad antes de realizar un downgrade. Evita duplicar dependencias o incorporar versiones incompatibles.

---

## Inicialización básica

Importa las clases necesarias en la `Activity` desde la cual se iniciará el flujo:

```kotlin
import com.alignet.payme.PaymeClient
import com.alignet.payme.PaymeClientDelegate
import com.alignet.payme.model.*
import com.alignet.payme.util.PaymeEnvironment
```

Inicializa el cliente, configura el ambiente e invoca el formulario de pago:

```kotlin
val paymeClient = PaymeClient(
    delegate = this,
    merchantCode = "MERCHANT_CODE"
)

paymeClient.setEnvironment(
    environment = PaymeEnvironment.DEVELOPMENT
)

paymeClient.invokeCaptureForm(
    from = this,
    sessionToken = "TOKEN_GENERADO_DESDE_BACKEND",
    paymeChargesRequest = paymeChargesRequest
)
```

- `merchantCode` es entregado por Alignet.
- `sessionToken` debe generarse desde el backend para la solicitud.
- `PaymeEnvironment.DEVELOPMENT` corresponde al ambiente de pruebas.
- `PaymeEnvironment.PRODUCTION` corresponde al ambiente productivo.

El objeto `paymeChargesRequest` debe contener la información de la transacción y la configuración del flujo. Consulta la documentación oficial para conocer su estructura completa. No incluyas credenciales reales ni secretos dentro de la aplicación.

---

## Métodos de pago

Dependiendo de la configuración del comercio, Pay-me SDK Android puede trabajar con:

- `CARD`
- `YAPE`
- `QR`
- `BANK_TRANSFER`
- `CUOTEALO`
- `PAGO_EFECTIVO`

La disponibilidad depende de los métodos de pago habilitados para el comercio.

---

## Callbacks

La `Activity` que inicia el flujo debe implementar `PaymeClientDelegate` y sus callbacks:

- `onRespondsPayme(...)`: devuelve el resultado del flujo.
- `onPaymeEvents(...)`: permite recibir eventos de interacción y navegación del SDK.

Consulta la [documentación oficial de inicialización](https://docs.pay-me.com/sdk-mobile/android/flujo-de-cobro/inicializar-pay-me-sdk-android) para conocer la firma documentada y el manejo de los callbacks.

---

## Seguridad

### Security

- El Access Token debe generarse desde el backend.
- No almacenes credenciales dentro de la aplicación.
- No expongas tokens o secretos en logs.
- No registres información sensible de pagos.
- Confirma el resultado final desde el backend cuando corresponda.

> **Los artefactos `.aar` del SDK son dependencias privadas y no deben publicarse en repositorios públicos.**

---

## Documentación

- [Documentación oficial: instalación](https://docs.pay-me.com/sdk-mobile/android/instalacion)
- [Requisitos](https://docs.pay-me.com/sdk-mobile/android/requisitos)
- [Inicialización](https://docs.pay-me.com/sdk-mobile/android/flujo-de-cobro/inicializar-pay-me-sdk-android)

Para información detallada, consulta [docs.pay-me.com](https://docs.pay-me.com/).

---

## Soporte

Para consultas relacionadas con integración, configuración, ambientes o certificación, contacta al equipo de Integraciones de Pay-me.
