UI Responsiveness

TEST UIResponsiveness
  UI_COMPONENT uiComponent = setupUIComponent(1024)
  ASSERT_TRUE(uiComponent.adjustsToScreenSize(1024), "UI should adjust to width of 1024 pixels")
END TEST

-------------------------------------------------------------------------------------------------------------
Refactorización

    //Service, en donde se hace mock para obener resultados
    public async Task<string> adjustsToScreenSize(int size)
    {
        // Valida size de componente
        string message = $"UI should adjust to width of {size.ToString()} pixels";
    }

    // Se pasa en bateria de hacer varias pruebas y se pasan por parametros
    [Theory]
    [InlineData(1024)]
    [InlineData(980)]
    [InlineData(512)]
    public async Task adjustsToScreenSize_Successful(int size)
    {
        // Ejecución de prueba
        var result = await UIService.adjustsToScreenSize(size);

        // Assert
        Assert.Equal($"UI should adjust to width of {size.ToString()} pixels", result);
    }
