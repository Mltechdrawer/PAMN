# Inflar en Android

En el contexto de la programación de aplicaciones móviles, particularmente en Android, el término **"inflar"** (*inflate* en inglés) se refiere al proceso de **convertir un archivo XML de diseño (layout)** en una **jerarquía de objetos de vista (`Views`) en memoria** que puede ser manipulada por la lógica de la aplicación.

Cuando se define la interfaz de usuario de una `Activity` o un `Fragment` en Android, generalmente se utiliza un archivo XML para describir los componentes visuales (botones, textos, imágenes, etc.). Estos archivos son simplemente descripciones de la interfaz en texto plano. Para que puedan ser mostrados en la pantalla, deben ser *inflados*, lo que implica que Android **convierte esa definición XML en objetos View concretos** (como `TextView`, `Button`, `ImageView`, etc.) que pueden interactuar con el código de la aplicación.

---

## 🧩 Ejemplo práctico

Considera un fragmento que tiene un archivo de diseño XML llamado `fragment_layout.xml`.  
Para que este fragmento muestre el diseño, necesita inflar el archivo XML en su ciclo de vida, específicamente en el método `onCreateView()`:

```kotlin
override fun onCreateView(  
    inflater: LayoutInflater,  
    container: ViewGroup?,  
    savedInstanceState: Bundle?  
): View? {  
    // 'Inflar' convierte el XML en una vista visible y manipulable
    return inflater.inflate(R.layout.fragment_layout, container, false)
}  
```