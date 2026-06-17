FROM php:8.2-cli

RUN apt-get update && apt-get install -y git unzip libzip-dev libpng-dev libonig-dev && docker-php-ext-install pdo pdo_mysql zip mbstring

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
WORKDIR /app

COPY composer.json ./
# 无 composer.lock 时需解析依赖；Composer 2.9+ 默认拦截含安全公告的包，演示项目暂关闭该策略
RUN composer config audit.block-insecure false \
    && composer install --no-dev --no-scripts --no-interaction
COPY . .
RUN mkdir -p bootstrap/cache storage/framework/{sessions,views,cache} storage/logs && chmod -R 775 bootstrap/cache storage
RUN composer dump-autoload --optimize
RUN cp .env.example .env 2>/dev/null || true && php artisan key:generate --no-interaction 2>/dev/null || true

EXPOSE 8000
CMD ["sh", "-c", "echo 'Waiting for MySQL...' && php scripts/wait-for-db.php && php artisan migrate --force && php artisan db:seed --force && exec php artisan serve --host=0.0.0.0 --port=8000"]
