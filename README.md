# ControleTransacaoCore
Transações distribuída  .NET CORE com Middleware

# Startup.cs

## Usage
```public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseHsts();
            }

            app.UseHttpsRedirection();
            app.UseTransactionMiddleware();
            app.UseMvc();
}



# TransactionMiddleware.cs

 public class TransactionMiddleware
    {
        private readonly RequestDelegate _next;

        public TransactionMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task Invoke(HttpContext httpContext)
        {
            var transaction = new TransactionScope(TransactionScopeOption.Required, new TransactionOptions { IsolationLevel = IsolationLevel.ReadCommitted, Timeout = TimeSpan.FromMinutes(2) });

            try
            {
                await _next(httpContext);
                transaction.Complete();
            }
            catch
            {
                Transaction.Current.Rollback();
            } finally
            {
                transaction.Dispose();
            }
      
            return;
        }
    }

    // Extension method used to add the middleware to the HTTP request pipeline.
    public static class TransactionMiddlewareExtensions
    {
        public static IApplicationBuilder UseTransactionMiddleware(this IApplicationBuilder builder)
        {
            return builder.UseMiddleware<TransactionMiddleware>();
        }
    }
    
 ```
