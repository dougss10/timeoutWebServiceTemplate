# timeoutWebServiceTemplate
esse Repositório contem um exemplo de configuração de timeout para um requisição utilizando webSreviceTemplate
/**
         *
         * Configuração para controlar o tempo de timeout de uma requisição do webserviceTemplate
         *
         */
        /** Configura o timeout da request **/
        RequestConfig.Builder requestBuilder = RequestConfig.custom();
        RequestConfig config = requestBuilder.setConnectTimeout(2000).setConnectionRequestTimeout(2000).build();

        /** configura o httpcliente que será utilizado no MessageSender do webServiceTemplate **/
        /** o encadeamento no comentario abaixo é necessaŕio pois quando se é utiliado um configuração desse genero
         * a request passa a dar erro pois tenta adicionar duas vezes no Header o content-length da request **/
        /** .addInterceptorFirst(new HttpComponentsMessageSender.RemoveSoapHeadersInterceptor()) **/
        CloseableHttpClient client = HttpClients
                .custom()
                .addInterceptorFirst(new HttpComponentsMessageSender.RemoveSoapHeadersInterceptor())
                .setDefaultRequestConfig(config)
                .build();

        /** cria o messageSender do webServiceTemplate **/
        HttpComponentsMessageSender messageSender = new HttpComponentsMessageSender(client);

        WebServiceTemplate wsTemplate = getWebServiceTemplate();
        wsTemplate.setMessageSender(messageSender);


        /** Para teste o funcionarmento do timeout configurado foi utilizado a seguinte lógica
         *
         *
           CalcPrecoPrazoResponse response = (CalcPrecoPrazoResponse) wsTemplate
                .marshalSendAndReceive("https://httpstat.us/200?sleep=5000", request,
                    new SoapActionCallback("http://tempuri.org/CalcPrecoPrazo"));

            no teste quanto configurado com um timeout menor que o da requisição ele gera o seguinte erro:
                org.springframework.ws.client.WebServiceIOException: I/O error: Read timed out; nested exception is java.net.SocketTimeoutException: Read timed out

         *
         * **/


        CalcPrecoPrazoResponse response = (CalcPrecoPrazoResponse) wsTemplate
                .marshalSendAndReceive("http://ws.correios.com.br/calculador/CalcPrecoPrazo.asmx", request,
                        new SoapActionCallback("http://tempuri.org/CalcPrecoPrazo"));
