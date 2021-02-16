from selenium import webdriver
import time

#si un jour envie de faire une interface graphique...
'''
from tkinter import *

master = Tk()
master.geometry("240x120+600+300")
var = IntVar()
var.set(1)

def quit_loop():
    print("Selection:",var.get())
    global selection
    selection = var.get()
    master.quit()

Label(master, text = "Quel type de commande voulez-vous ?").grid(row=0)
Radiobutton(master, text = "A emporter", variable=var, value = 1).grid(row=1)
Radiobutton(master, text = "Livraison", variable=var, value = 2).grid(row=2)
Button(master, text = "Valider", command=quit_loop).grid(row=3)

master.mainloop()

if selection == 1:
	type_commande="emporter"
else:
	type_commande="livraison"
'''

ville = "Lyon"   #ville dans laquelle on cherche les codes
#!!!! changer le xpath du pop up d'entrée suivant la ville
chromedriver_location = "chromedriver"    #emplacement de chromedriver.exe si vous êtes sous windows

driver = webdriver.Chrome(chromedriver_location)
#adresses des boutons sur le site de dominos
a_emporter = '//*[@id="wrapper"]/div[4]/div/div/a[2]'
ville_ou_code_postal = '//*[@id="customer-suburb"]'
lyon1 = '//*[@id="divStoreSearch"]/section/ul/li[1]/a'
code_reduc = '//*[@id="voucher_code"]'
valider_code_reduc = '//*[@id="apply_voucher"]'

def clique_a_emporter():      #clique sur "à emporter"
	try:
		driver.find_element_by_xpath(a_emporter).click()
	except:
		time.sleep(2)
		clique_a_emporter()

def clique_boîte_ville():   #clique sur la boîte de dialogue "ville"
	try:
		driver.find_element_by_xpath(ville_ou_code_postal).click()
	except:
		time.sleep(2)
		clique_boîte_ville()

def clique_sur_ville():     #clique sur la première ville trouvée
	try:
		driver.find_element_by_xpath(lyon1).click()
	except:
		time.sleep(2)
		clique_sur_ville()

def slection_horaire():   #sélectionne une horaire pour la commande
	try:
		driver.find_element_by_xpath('//*[@id="order_time_select"]').click()
		time.sleep(0.5)
		driver.find_element_by_xpath('/html/body/div[5]/div[2]/form/section[3]/div[2]/div/select/option[8]').click()
		time.sleep(0.5)
		driver.find_element_by_xpath('//*[@id="start-order-button"]').click()
	except:
		print("\npas besoin de séléctionner d'horaire")
		pass

loop_ajout_pizza=0
def ajout_pizza():   #ajoute une pizza au panier
	global loop_ajout_pizza
	if loop_ajout_pizza<3:
		try:
			driver.find_element_by_xpath('/html/body/div[8]/section/section/div[3]/section[1]/div[3]/div[2]/section/div[2]/div/div/div/form/div[4]/div/div[2]/button').click()
			time.sleep(2)
		except:
			time.sleep(1)
			loop_ajout_pizza+=1
			ajout_pizza()
	else:
		loop_ajout_pizza=0

loops_clic_code_reduc=0
def clic_code_reduc():    #clique sur la boîte de dialogue du code de réduction
	global loops_clic_code_reduc
	if loops_clic_code_reduc<3:
		try:
			driver.find_element_by_xpath(code_reduc).click()
		except:
			time.sleep(2)
			loops_clic_code_reduc+=1
			close_answer()     #si le selenium ne trouve pas la boîte de dialogue, elle est peut-être cachée
			print("fail clique boîte code reduc")
			clic_code_reduc()
	else:
		loops_clic_code_reduc=0

def clic_valider():   #clique sur valider
	try:
		driver.find_element_by_xpath(valider_code_reduc).click()
	except:
		time.sleep(2)
		try:
			driver.find_element_by_xpath(valider_code_reduc).click()
		except:
			close_answer()    #si le selenium ne trouve pas le bouton valider, il est peut-être cachée

def close_entrance_pop_up():    #ferme la pub en arrivant sur le site s'il y en a une
	time.sleep(3)
	try:
		driver.find_element_by_xpath('//*[@id="offer-addtoyourorder-no-8794"]').click()
	except:
		pass

loops_close_answer=0
def close_answer():   #ferme le message de dominos
	global loops_close_answer
	if loops_close_answer<3:
		try:
			driver.find_element_by_xpath('//*[@id="validation_close_button"]').click()
		except:
			time.sleep(1)
			print("echec de fermeture du pop-up")
			loops_close_answer+=1
			close_answer()
	else:
		loops_close_answer=0
 
def clear_basket():   #clear le panier et les codes
	while True:  #enlève les promos
		try:
			driver.find_element_by_xpath('/html/body/div[8]/section/section/div[3]/section[2]/div/div[4]/div/div[1]/div[1]/div[2]/a').click()
			time.sleep(3)
		except:
			break
	while True:  #enlève les produits (pizzas, boissons, etc... qui ont pu s'ajouter suite au code promo)
		try:
			driver.find_element_by_xpath('/html/body/div[8]/section/section/div[3]/section[2]/div/div[4]/div/div[1]/div[4]/a[1]').click()
			time.sleep(3)
		except:
			break
with open("code_actuel.txt","r") as c_a:   #le dernier code testé est stocké dans un fichier
	code=int(c_a.read())

def essai_des_codes():
	global code   #global c'est peut-être pas maxi opti mais blc
	temoin_erreur=0  #variable en cas de bug de co
	besoin_reboot=0  #idem
	while True:
		clic_code_reduc()
		driver.find_element_by_xpath(code_reduc).clear()  #clear la boîte de dialogue
		driver.find_element_by_xpath(code_reduc).send_keys(str(code))     #essaie un code de réduction
		clic_valider()
		time.sleep(1)
		answer = driver.find_element_by_id('validation_body').text    #check la réponse de Dominos
		if f"Ce coupon {code} ne peut pas être utilisé à ce moment de la journée" in answer:
			besoin_reboot=0
			with open("log_moment.txt","a+") as log_moment:
				log_moment.write(f"{code}\n")
		elif answer=="":
			try:
				time.sleep(1)
				print(f"\n {code}\n")
				time.sleep(1)
				code_reduc_type = driver.find_element_by_xpath('/html/body/div[8]/section/section/div[3]/section[2]/div/div[4]/div/div[1]/div[1]/div[1]/div[1]').text.strip("COUPON: ")  #récupère ce que fait le coupon
				print(f"{code_reduc_type}\n")
				with open(f"log_bueno.txt","a+") as log_bueno:   #stock tous les coupons et leurs effets dans un fichier texte
					log_bueno.write(f"\n\n{code}")
					log_bueno.write(f"   {code_reduc_type}")
				clear_basket()
				time.sleep(2)
				ajout_pizza()
				besoin_reboot=0
			except:
				if besoin_reboot<2:
					if temoin_erreur<2:
						print("faux positif 2")
						temoin_erreur+=1
						code-=1
					else:
						temoin_erreur=0
						besoin_reboot+=1
				else:
					driver.refresh()   #rafraichît la page
					print("Temps de réponse trop long, rafraichissement de la page...")
					time.sleep(3)
					essai_des_codes()
		else:
			besoin_reboot=0

					
		close_answer()
		with open("log.txt","a+") as log:   #stocke TOUS les codes et leurs réponses dans un log
			log.write(f"\n\n{code}\n")
			log.write(answer)
		code+=1     #actualise le code à tester (+1)

		with open("code_actuel.txt", "w") as code_actuel:   #enregistre le code actuel
			code_actuel.write(str(code))


#partie du code qui s'exécute
driver.get("https://www.dominos.fr")    #ouvre le site de Dominos
clique_a_emporter()
clique_boîte_ville()
driver.find_element_by_xpath(ville_ou_code_postal).send_keys(ville)    #rentre un code postal dans la boîte
clique_sur_ville()
slection_horaire()
close_entrance_pop_up()
ajout_pizza()
essai_des_codes()
