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
}

// Violacion de SOLID

1 Misma clase realiza varias resposablidades, debe haber delegacion para BD y para envio de email (SRP)
2 No es flexible por que hay cosas especificas por ejemplo en el pago esta express y standard y si se agrega uno nuevo se tiene que modificar (OCP)
3 Debe existir subclases, como en el caso de pago, hacen lo mismo la diferenciación esta en prioridad(LSP)
4 Creacion de Interfaces(ISP)
5 Como todo existe en la misma clase, deberia haber interfaces para limitar resposabilidades(DIP)



