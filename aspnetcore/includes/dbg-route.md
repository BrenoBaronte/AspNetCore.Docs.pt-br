## <a name="debug-diagnostics"></a><span data-ttu-id="4c6a6-101">Diagnóstico de depuração</span><span class="sxs-lookup"><span data-stu-id="4c6a6-101">Debug diagnostics</span></span>

<span data-ttu-id="4c6a6-102">Para obter uma saída de diagnóstico de roteamento detalhada, defina `Logging:LogLevel:Microsoft` como `Debug` .</span><span class="sxs-lookup"><span data-stu-id="4c6a6-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="4c6a6-103">No ambiente de desenvolvimento, defina o nível de log em *appsettings.Development.jsem*:</span><span class="sxs-lookup"><span data-stu-id="4c6a6-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
