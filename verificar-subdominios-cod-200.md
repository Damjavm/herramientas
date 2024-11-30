**SCRIPT HECHO EN PYTHON PARA VERIFICAR EL STATUS COD 200 EN UNA LISTA DE SUBDOMINIOS**

```
import argparse
import requests
import time

def check_subdomains(file_path, output_file=None):
    try:
        with open(file_path, 'r') as file:
            subdomains = [line.strip() for line in file.readlines()]
        
        valid_subdomains = []  # Lista para almacenar subdominios con código 200
        print(f"Verificando {len(subdomains)} subdominios...\n")
        
        for subdomain in subdomains:
            # Asegurarse de que el subdominio no tenga esquema
            if subdomain.startswith("http://") or subdomain.startswith("https://"):
                subdomain = subdomain.split("://", 1)[1]  # Eliminar esquema si existe
            
            urls_to_try = [f"https://{subdomain}", f"http://{subdomain}"]  # Intentar ambos esquemas
            for url in urls_to_try:
                try:
                    response = requests.get(url, timeout=10)
                    if response.status_code == 200:
                        print(f"[200 OK] {url}")
                        valid_subdomains.append(url)  # Añadir a la lista si es código 200
                        break  # Si el primer esquema funciona, no intentamos el segundo
                except requests.exceptions.RequestException:
                    continue  # Si falla, probar con el siguiente esquema
            
            time.sleep(0.1)  # Agregar pausa para evitar bloqueos (ajusta según sea necesario)

        # Si se especifica un archivo de salida, guardar la lista de subdominios válidos
        if output_file:
            with open(output_file, 'w') as outfile:
                outfile.write("\n".join(valid_subdomains))
            print(f"\nSe han exportado {len(valid_subdomains)} subdominios válidos a '{output_file}'.")
    except FileNotFoundError:
        print(f"El archivo '{file_path}' no fue encontrado.")
    except Exception as e:
        print(f"Ocurrió un error: {e}")

def main():
    parser = argparse.ArgumentParser(description="Verifica el estado HTTP de una lista de subdominios.")
    parser.add_argument('-d', '--domains', type=str, required=True, help="Ruta del archivo con la lista de subdominios.")
    parser.add_argument('-o', '--output', type=str, help="Nombre del archivo para exportar subdominios válidos (opcional).")
    args = parser.parse_args()

    check_subdomains(args.domains, args.output)

if __name__ == "__main__":
    main()
```

**Instrucciones**

Copia el script y haz un nuevo archivo, por ejemplo ```verificar_subdominios.py```

Se usa de la siguiente manera: 

```python verificar_subdominios.py -d lista_de_subdominios.txt -o subdominios_cod_200_exportados.txt```

**-d** para indicar la lista de subdominios.

**-o** para guardar los resultados en un archivo.
