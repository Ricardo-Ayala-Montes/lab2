User Authentication Tests

TEST UserAuthentication
  ASSERT_TRUE(authenticate("validUser", "validPass"), "Should succeed with correct credentials")
  ASSERT_FALSE(authenticate("validUser", "wrongPass"), "Should fail with wrong credentials")
END TEST

-------------------------------------------------------------------------------------------------------------
Refactorización

    //Service, en donde se hace mock para obener resultados
    public async Task<bool> Authenticate(string username, string password)
    {
        // Valida credenciales del mock
        var user = await _userRepository.ValidateUserCredentials(username, password);
        if (user == null)
        {
            return false;
        }
        else
        {
            return true;
        }
    }

    // Se inyecta mock en donde contiene
           mockAuth.Setup(x => x.ValidateUserCredentials(username, password))
            .ReturnsAsync(user);


    private readonly Mock<IAuthenticate> mockAuth;

   [Fact]
    public async Task Authentication_Successful()
    {
        // datos con los que se probará test
        var username = "user1";
        var password = "password123";

        // Ejecución de prueba
        var result = await authenticationService.Authenticate(username, password);

        // Assert
        Assert.Equal(true, result);
    }

    [Fact]
    public async Task Authentication_Unsuccessful()
    {
        // datos con los que se probará test
        var username = "user2";
        var password = "password456";

        // Ejecución de prueba
        var result = await authenticationService.Authenticate(username, password);

        // Assert
        Assert.Equal(false, result);
    }

    [Fact]
    public async Task Authentication_Exception()
    {
        // datos con los que se probará test
        var username = "user2";
        var password = "password456";

        // Ejecución de prueba
        // Considetando que los errores son controlados
        var result = await Assert.ThrowsAsync<Exception>(() => authenticationService.Authenticate(username, password));

        // Assert
        Assert.Equal("Error", response.Message.ToString());
    }
