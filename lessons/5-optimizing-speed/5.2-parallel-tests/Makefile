.PHONY: build deps composer-install composer-update composer reload test parallel-test run-tests run-parallel-tests start stop destroy doco rebuild start-local

current-dir := $(dir $(abspath $(lastword $(MAKEFILE_LIST))))

build: deps start

deps: composer-install

# 🐘 Composer
composer-install: CMD=install
composer-update: CMD=update
composer composer-install composer-update:
	@docker run --rm --interactive --user $(id -u):$(id -g) \
		--volume $(current-dir):/app \
		--volume ${COMPOSER_HOME:-$HOME/.composer}:/tmp \
		clevyr/prestissimo $(CMD) \
			--ignore-platform-reqs \
			--no-ansi \
			--no-interaction

reload:
	@docker-compose exec php-fpm kill -USR2 1
	@docker-compose exec nginx nginx -s reload

test:
	@docker exec codelytv-php_ddd_skeleton-php make run-tests

parallel-test:
	@docker exec codelytv-php_ddd_skeleton-php make run-parallel-tests

run-tests:
	mkdir -p build/test_results/phpunit
	./vendor/bin/phpunit --exclude-group='disabled' --log-junit build/test_results/phpunit/junit.xml tests
	./vendor/bin/behat -p mooc_backend --format=progress -v
