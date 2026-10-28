# Aula 20 - Geolocalização Jonas

Aplicativo Flutter desenvolvido na aula 20 para demonstrar:

- leitura da localização atual com `geolocator`;
- leitura da orientação do dispositivo com `flutter_device_compass`;
- visualização de latitude, longitude e altitude;
- bússola desenhada com `CustomPainter`;
- cadastro visual, navegação e exclusão de waypoints.

## Executar

```bash
flutter pub get
flutter run
```

No Android, autorize o acesso à localização quando solicitado. O projeto já
contém as permissões de localização fina e aproximada no manifesto.

## Validação

```bash
dart analyze
```
