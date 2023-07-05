share ?= $$HOME/current

build:
	sudo docker build --build-arg userid=$(shell id -u) \
	    --build-arg groupid=$(shell id -g) \
	    --build-arg username=$(shell id -un) \
	    -t kali-img .

run:
	sudo docker run -it --rm --name kali-$(shell date +%s) \
		--hostname kali \
		-v $(share):/share \
		-v $(share)/logs:/logs/ \
		kali-img
