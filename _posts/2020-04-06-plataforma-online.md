---
layout: post
current: post
cover: assets/images/1.png
navigation: True
title: Plataforma Online
date: 2020-10-05 10:18:00
tags: 2-Bachillerato
class: post-template
subclass: 'post'
author: aalmeidar
---
# Objetivo
El objetivo de esta tarea era crear una [presentación](https://docs.google.com/presentation/d/1v7hsYPunWb1t4YzMx1KyDZVvCrEYhKNUDfs4ckhcieA/edit?usp=sharing), a tráves de la cual, mostramos a nuestros alumnos como proceder en caso de situación de confinamiento.

Para ello, teniamos que tener en cuenta el nivel de los alumnos a los que nos dirigiamos, además de revisar todas las hipotéticas situaciones que se presenten.

# Trabajo 

Mi grupo, después de plantear y probar varias alternativas, decidió optar por la plataforma web educativa [Classroom](https://classroom.google.com/). Para comunicarnos con los alumnos usamos la aplicación [Discord](https://discord.com/) que nos permite llamadas, mensajería, etc. Por último, para ofrecer alguna práctica a los alumnos recurrimos al recurso de [Kahoot](https://kahoot.com/). En estos tres pilares se fundamentó nuestra metodología que usariamos con nuestros alumnos en situación de confinamiento. 

Añadimos algunos extras para facilitar el trabajo a los alumnos, por ejemplo, elaboramos un bot en el servidor de Discord programado en Python que comunicaba notas y realizaba algunas labores del profesorado. 

Este trabajo nos puso en la piel de los docentes durante el confinamiento, fue un proyecto sin mucha dificultad pero proponia la reflexión sobre que metódo usar para contactar con el alumnado.

## Bot

```python
import discord
from discord.ext import commands
import datetime
import urllib
from urllib import parse, request
import re
import random
import statistics as stats

bot = commands.Bot(command_prefix='>', description="This is a heleper bot")
notas_alumno1_m= []
notas_alumno1_dt= []
notas_alumno1_h= []
asignaturas = {'Matemáticas': notas_alumno1_m, 'Dibujo Técnico': notas_alumno1_dt, 'Historia': notas_alumno1_h}
bbdd={'Alumno1':asignaturas}

asignaturas = {}
temas = {}
user=[]
notas=[]


@bot.command()
async def o(ctx, nombre):
user = nombre
await ctx.send(f'{user}')


@bot.command()
async def nota(ctx, alumno, asignatura, nota: float):

if nota<=10 and nota>=0:
if alumno == 'Alumno1' or alumno == 'alumno1':
if asignatura == 'Matemáticas':
notas_alumno1_m.append(nota)
await ctx.send('Nota asignada con éxito')
if asignatura == 'Dibujo Técnico':
notas_alumno1_dt.append(nota)
await ctx.send('Nota asignada con éxito')
if asignatura == 'Historia':
notas_alumno1_h.append(nota)
await ctx.send('Nota asignada con éxito')
else:
await ctx.send("[*] Ingrese correctamente el alumno")
else:
await ctx.send("[*] Ingrese correctamente la nota")
print(bbdd)

@bot.command()
async def media(ctx, alumno, asignatura):
Media = 0
if alumno == 'alumno1' or alumno == 'Alumno1':
if asignatura == 'Matemáticas' or asignatura == 'matemáticas':
Media = stats.mean(notas_alumno1_m)
if asignatura == 'Dibujo Técnico' or asignatura == 'dibujo técnico' or asignatura == 'Dibujo técnico' :
Media == stats.mean(notas_alumno1_dt)
if asignatura == 'Historia' or asignatura == 'historia':
Media == stats.mean(notas_alumno1_h)
embed = discord.Embed(title=f"Media de {alumno} de {asignatura}", description=f"{Media}", color=discord.Color.dark_gold())
await ctx.send(embed=embed)

@bot.command()
async def ping(ctx):
await ctx.send('pong')

@bot.command()
async def sum(ctx, numOne: int, numTwo: int):
await ctx.send(numOne + numTwo)

@bot.command()
async def info(ctx):
embed = discord.Embed(title=f"{ctx.guild.name}", description="Lorem Ipsum", timestamp=datetime.datetime.utcnow(), color=discord.Color.blue())
embed.add_field(name="Server created at", value=f"{ctx.guild.created_at}")
embed.add_field(name="Server Owner", value=f"{ctx.guild.owner}")
embed.add_field(name="Server Region" , value=f"{ctx.guild.region}")
embed.add_field(name="Server ID", value=f"{ctx.guild.id}")
embed.set_thumbnail(url="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/Python-logo-notext.svg/1200px-Python-logo-notext.svg.png")
await ctx.send(embed=embed)

@bot.command()

async def youtube(ctx, *, search):
query_string = parse.urlencode({'search_query': search})
html_content = request.urlopen('http://www.youtube.com/results?' + query_string)
# print(html_content.read().decode())
search_results = re.findall(r"watch\?v=(\S{11})", html_content.read().decode())
print(search_results)
# I will put just the first result, you can loop the response to show more results
await ctx.send('https://www.youtube.com/watch?v=' + search_results[0])

@bot.command()
async def mis_notas(ctx, alumno, asignatura):
notas = 0
if alumno=='Alumno1' or alumno == 'alumno1':
if asignatura == 'Matemáticas':
notas = notas_alumno1_m
if asignatura == 'Dibujo Técnico':
notas = notas_alumno1_dt
if asignatura == 'Historia':
notas = notas_alumno1_h
embed = discord.Embed(title=f"Notas de {alumno} de {asignatura}", description=f"{notas}", color=discord.Color.dark_gold())
await ctx.send(embed=embed)



#Events

@bot.event
async def on_ready():
await bot.change_presence(activity=discord.Streaming(name="Tutorials", url="https://meet.google.com/fvg-opqi-idw"))
print('My bot is ready')

bot.run('')

```



## Enlaces de interés

- [Presentaciones](https://fernandosax.wordpress.com/2020/09/16/2o-bach-tic-plataforma-online/)
- [Noticia: ¿Cómo afectó el Confinamiento a estudiantes y profesores?](https://mextudia.com/como-afecto-el-confinamiento-a-estudiantes-y-profesores/)


