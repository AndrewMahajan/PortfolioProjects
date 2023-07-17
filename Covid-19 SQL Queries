/*
Covid 19 Data Exploration 

Skills used in queries: Joins, CTE's, Temp Tables, Windows Functions, Aggregate Functions, Creating Views, Converting Data Types

*/

--Select Data that we are going to be using
SELECT * 
FROM PortfolioProject..CovidDeaths
Where continent is not null
order by 3,4


SELECT location,date,total_cases,new_cases,total_deaths,population
FROM PortfolioProject..CovidDeaths
Where continent is not null
order by 1,2

-- Looking at Total Cases vs. Total Deaths
--shows likelihood of dying if you contract covid in your country

SELECT location,date,total_cases,total_deaths, (total_deaths/total_cases) *100 as DeathPercentage
FROM PortfolioProject..CovidDeaths
Where location like '%states%'
and continent is not null
order by 1,2

--Looking at Total Cases vs. Population
--Shows what percentage of population got covid
SELECT location,date,total_cases,population, (total_cases/population) *100 as PercentagePopulationInfected
FROM PortfolioProject..CovidDeaths
Where location like '%states%'
order by 1,2

--Looking at Countries with Highest Infection rate compared to Population

SELECT location,population, MAX(total_cases) as HighestInfectionCount, MAX((total_cases/population)) *100 as PercentagePopulationInfected
FROM PortfolioProject..CovidDeaths
Group by location,population
order by PercentagePopulationInfected desc

--Showing Countries with Highest Death Count per Population

SELECT location, MAX(cast(Total_deaths as int)) as TotalDeathCount
FROM PortfolioProject..CovidDeaths
Where continent is not null
Group by location
order by TotalDeathCount desc

-- BREAKDOWN BY CONTINENT

--Showing the continents with the highest death count per population

SELECT continent, MAX(cast(Total_deaths as int)) as TotalDeathCount
FROM PortfolioProject..CovidDeaths
Where continent is not null
Group by continent
order by TotalDeathCount desc

--GLOBAL NUMBERS

SELECT SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths,SUM(cast(New_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
FROM PortfolioProject..CovidDeaths
where continent is not null
order by 1,2

--Looking at Total Population vs Vaccinations

Select dea.continent,dea.location,dea.date, dea.population,vac.new_vaccinations
,SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.location Order by dea.location, dea.date) as RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths dea
JOIN PortfolioProject..CovidVaccinations vac
ON dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
order by 2,3

--USE CTE

With PopvsVac (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated)
as
(
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 
)
Select *, (RollingPeopleVaccinated/Population)*100
From PopvsVac

--TEMP TABLE

DROP Table if exists #PercentPopulationVaccinated
Create Table #PercentPopulationVaccinated
(
Continent nvarchar(255),
Location nvarchar(255),
Date datetime,
Population numeric,
New_vaccinations numeric,
RollingPeopleVaccinated numeric
)

Insert into #PercentPopulationVaccinated
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
--where dea.continent is not null 
--order by 2,3

Select *, (RollingPeopleVaccinated/Population)*100
From #PercentPopulationVaccinated

-- Creating View to store data for later visualizations

Create View PercentPopulationVaccinated as
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 
