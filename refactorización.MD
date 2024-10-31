class SystemManager {
    processOrder(order) {
        if (order.type == "standard") {
            verifyInventory(order);
            processStandardPayment(order);
        } else if (order.type == "express") {
            verifyInventory(order);
            processExpressPayment(order, "highPriority");
        }
        updateOrderStatus(order, "processed");
        notifyCustomer(order);
    }

    verifyInventory(order) {
        // Checks inventory levels
        if (inventory < order.quantity) {
            throw new Error("Out of stock");
        }
    }

    processStandardPayment(order) {
        // Handles standard payment processing
        if (paymentService.process(order.amount)) {
            return true;
        } else {
            throw new Error("Payment failed");
        }
    }

    processExpressPayment(order, priority) {
        // Handles express payment processing
        if (expressPaymentService.process(order.amount, priority)) {
            return true;
        } else {
            throw new Error("Express payment failed");
        }
    }

    updateOrderStatus(order, status) {
        // Updates the order status in the database
        database.updateOrderStatus(order.id, status);
    }

    notifyCustomer(order) {
        // Sends an email notification to the customer
        emailService.sendEmail(order.customerEmail, "Your order has been processed.");
    }


    // Se crean 4 Interfaces para delimitar responsabilidades
    public interface IPayment
	{
        Process(order);
	}
    public interface IOrder
    {
        UpdateStatus(order);
    }
    public interface IProducts
    {
        verifyInventary(order);
    }

    public interface INotification
    {
        SendCustomer(order);
    }
}

// clase que implementa IPayment
class Payment : IPayment
{
    Process(order)
    {
        //Lógica para identificar  tipo de orden para hacerlo standard o express
        if (order.type == "standard") {
            ....
            processAmount(order.amount, "standard");
        }
        else{
            ....
            processAmount(order.amount, "express");
        }
    }

    // metodo privado donde se pasa la prioridad
    private processAmount(order, priority)
    {
        if (expressPaymentService.process(order.amount, priority)) {
            return true;
        } else {
            throw new Error($"{priority} failed");
        }
    }
}
//clase que implementa Iorder
class Order : IOrder
{
    private readonly IDatabase database;

    public Notification(IDatabase database)
    {
        //Se pasa una interfaz para hacer operaciones de BD
        this.database = database;

    };
    // Método para actualizar status
    UpdateStatus(order)
    {
        database.updateOrderStatus(order.id, order.status);
    }
}

//clase que implementa IProducts
class Products : IProducts
{
    // Método para verificar invdntario de productos
    verifyInventary(order)
    {
        if (inventory < order.quantity) {
            throw new Error("Out of stock");
        }
    }
}

//clase que implementa INotification
class Notification : INotification
{
    // servicio de email
    private readonly IEmailService emailService;

    public Notification(IEmailService emailService)
    {
        this.emailService = emailService;
    };
    //Método de envio de notificación
    SendCustomer(order)
    {
        this.emailService.sendEmail(order.customerEmail, "Your order has been processed.");
    }
}



class SystemManager {

    private readonly IPayment payment;
    private readonly IOrder order;
    private readonly INotification notification;
    private readonly IProducts products;

    //Inyectan las dependencias
    public SystemManager(IPayment paymentExpress,
        IOrder order,
        INotification notification,
        IProducts products)
    {
        this.paymentExpress = paymentExpress;
        this.order = order;
        this.notification = notification;
        this.products = products;
    }

    processOrder(order) {
        //Se ejecutan siempre verificación de inventario y se procesa el pago
        this.products.verifyInventory(order);
        this.payment.process(order);
        this.order.UpdateStatus(order);
        this.notification.SendCustomer(order);
    }