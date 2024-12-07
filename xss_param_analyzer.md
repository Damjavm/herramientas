**¿Qué hace este script?**

Lee el archivo de entrada: Procesa una lista de URLs, una por línea.

Identifica parámetros: Verifica si la URL tiene parámetros en la query string.

Exporta solo las URLs con parámetros: Las URLs con parámetros se guardan en el archivo de salida, una por línea, para analizar si son vulnerables a XSS con herramientas externas o manualmente.

```
import argparse
from urllib.parse import urlparse, parse_qs

def analizar_urls(input_file, output_file):
    """
    Analiza una lista de URLs para identificar aquellas con parámetros potencialmente vulnerables a XSS.
    """
    try:
        with open(input_file, 'r') as f:
            urls = f.readlines()
        
        urls_vulnerables = []

        for url in urls:
            url = url.strip()
            if not url:
                continue

            # Parseamos la URL
            parsed_url = urlparse(url)
            parametros = parse_qs(parsed_url.query)

            # Si hay parámetros, se marca como potencialmente vulnerable
            if parametros:
                urls_vulnerables.append({
                    "url": url,
                    "parametros": list(parametros.keys())
                })

        # Exportar las URLs con parámetros a un archivo para XSStrike
        with open(output_file, 'w') as f:
            for entrada in urls_vulnerables:
                f.write(f"{entrada['url']}\n")

        print(f"Análisis completo. Se identificaron {len(urls_vulnerables)} URLs con parámetros.")
        print(f"Lista exportada a '{output_file}'.")
    except FileNotFoundError:
        print(f"El archivo '{input_file}' no se encontró. Verifica la ruta e inténtalo de nuevo.")
    except Exception as e:
        print(f"Se produjo un error: {e}")

def main():
    parser = argparse.ArgumentParser(description="Analiza URLs en busca de parámetros potencialmente vulnerables a XSS.")
    parser.add_argument("-f", "--file", required=True, help="Ruta al archivo de entrada con la lista de URLs.")
    parser.add_argument("-o", "--output", required=True, help="Ruta al archivo de salida para las URLs vulnerables.")
    args = parser.parse_args()

    analizar_urls(args.file, args.output)

if __name__ == "__main__":
    main()
```

**Cómo usarlo**

Guarda el script como ```xss_param_analyzer.py```

Asegúrate de tener una lista de URLs en un archivo, por ejemplo, ```lista_urls.txt```

Ejecuta el script:

```python xss_param_analyzer.py -f lista_urls.txt -o lista_para_xss.txt```
