# Refactorización de Patrones Estructurales (GoF) - Lógica condicional if (esHoja) en múltiples lugares

## Código base mal estructurado (antes del refactor)
```csharp
using System;
using System.Collections.Generic;
using System.Linq;

// Clase Tema con lógica if(esHoja) dispersa
public class Tema
{
    private bool esHoja;
    private string titulo;
    private double duracion;
    private List<Tema> subtemas;

    public Tema(bool esHoja, string titulo, double duracion, List<Tema> subtemas)
    {
        this.esHoja   = esHoja;
        this.titulo   = titulo;
        this.duracion = duracion;
        this.subtemas = subtemas;
    }

    public void Imprimir()
    {
        if (esHoja)
        {
            Console.WriteLine($"• {titulo} ({duracion}h)");
        }
        else
        {
            Console.WriteLine(titulo);
            foreach (var hijo in subtemas)
            {
                hijo.Imprimir();
            }
        }
    }

    public double GetDuracionTotal()
    {
        if (esHoja)
            return duracion;

        return subtemas.Sum(h => h.GetDuracionTotal());
    }

    public int ContarHojas()
    {
        if (esHoja)
            return 1;

        return subtemas.Sum(h => h.ContarHojas());
    }
}
```
## Refactor con patrón Composite
```csharp
using System;
using System.Collections.Generic;
using System.Linq;

// Definición de la abstracción
public abstract class Tema
{
    protected string Titulo { get; }

    protected Tema(string titulo)
    {
        Titulo = titulo;
    }

    public abstract void Imprimir();
    public abstract double GetDuracionTotal();
    public abstract int ContarHojas();
}

// Nodo hoja del árbol
public class TemaHoja : Tema
{
    private readonly double duracion;

    public TemaHoja(string titulo, double duracion)
        : base(titulo)
    {
        this.duracion = duracion;
    }

    public override void Imprimir()
    {
        Console.WriteLine($"• {Titulo} ({duracion}h)");
    }

    public override double GetDuracionTotal()
    {
        return duracion;
    }

    public override int ContarHojas()
    {
        return 1;
    }
}

// Nodo compuesto con lista de subtemas
public class TemaCompuesto : Tema
{
    private readonly List<Tema> subtemas = new List<Tema>();

    public TemaCompuesto(string titulo)
        : base(titulo)
    {
    }

    public void AddSubtema(Tema subtema)
    {
        subtemas.Add(subtema);
    }

    public override void Imprimir()
    {
        Console.WriteLine(Titulo);
        foreach (var sub in subtemas)
        {
            sub.Imprimir();
        }
    }

    public override double GetDuracionTotal()
    {
        return subtemas.Sum(s => s.GetDuracionTotal());
    }

    public override int ContarHojas()
    {
        return subtemas.Sum(s => s.ContarHojas());
    }
}
```
