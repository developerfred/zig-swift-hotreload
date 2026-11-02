# Zig Swift Hot-Reload

Recarregamento automático de código Zig e SwiftUI durante desenvolvimento — sem necessidade de reiniciar o aplicativo.

## Problema

Durante desenvolvimento de apps Zig + SwiftUI, toda mudança no código requer:
1. Parar o app
2. Recompilar Zig (.dylib)
3. Recompilar Swift
4. Iniciar o app novamente

Este ciclo é lento e interrompe o fluxo de desenvolvimento.

## Solução

Hot-reload automático que detecta mudanças nos arquivos-fonte e recarrega:
- Biblioteca Zig (via `dlclose`/`dlopen` dinâmico)
- Visualizações SwiftUI (via injeção de código)
- Estado do agente (preservado entre recargas)

## Estrutura

```
zig-swift-hotreload/
├── README.md
├── LICENSE
├── Package.swift
├── Sources/
│   ├── HotReload/             # Código Swift
│   │   ├── FileWatcher.swift  # Observa mudanças nos arquivos
│   │   ├── ZigReloader.swift  # Recarrega .dylib dinamicamente
│   │   └── SwiftUIReload.swift# Injeta mudanças na UI
│   └── zig/
│       └── hotreload.zig      # Suporte a recarga no Zig
├── Tests/
│   └── HotReloadTests/
│       ├── FileWatcherTests.swift
│       └── ZigReloaderTests.swift
└── example/
    ├── zig/
    │   └── hotreload-example.zig
    └── swift/
        └── main.swift
```

## Uso

```swift
import HotReload

// Configura observador de arquivos
let watcher = FileWatcher(
    paths: ["zig-core/src/", "swift-app/Sources/"],
    debounce: 0.5
)

// Callback quando arquivos mudam
watcher.onChange = { changedFiles in
    print("Arquivos alterados: \(changedFiles)")
    
    // Recarrega biblioteca Zig
    try ZigReloader.reload("libvanderley-core.dylib")
    
    // Notifica SwiftUI para redesenhar
    SwiftUIReload.notify()
}

// Inicia observação
try watcher.start()
```

## Funcionalidades

- ✅ Watch de arquivos com debounce
- ✅ Recarga dinâmica de .dylib (dlclose/dlopen)
- ✅ Injeção de código SwiftUI sem reiniciar
- ✅ Preservação de estado entre recargas
- ✅ Suporte a múltiplos diretórios de origem
- ✅ Log detalhado de mudanças

## Requisitos

- macOS 15+
- Swift 6+
- Zig 0.17.0+

## Licença

MIT
